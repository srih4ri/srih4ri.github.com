--- 
layout: post
title: Fuzzy full text search using redis
published: true
---
This is my try at text search described [here](http://playnice.ly/blog/2010/05/05/a-fast-fuzzy-full-text-index-using-redis/)

There is already redis running in my app as the back end for Rails Cache.The new requirement was the ability to search posts by user.

I wanted to make better use of the redis server that is already running (and is under utilized by the Rails cache).

We first build index of all the content we want to search.That is , like the index you find at the back of a book , all the page numbers where a term can be found.In my case , the page numbers are the id of the post in the database.

{% highlight ruby %}
Post {:id => 1 , :content => 'Can we go now?'}
Post {:id => 2 ,:content => 'We are here to arrest you'}
{% endhighlight %}

My index would be  :

{% highlight ruby %}
'Can' => [1]
'We' => [1,2]
{% endhighlight %}
...
and so on

Redis Sets can be used for this purpose , we need to use the following from redis :

*    To add an item into a set

{% highlight ruby %}
redis.sadd 'name_of_a_set', item 
{% endhighlight %}


* To retrieve an item that is in the intersection of set1 and set2

{% highlight ruby %}
redis.sinter 'name_of_set_1' 'name_of_set_2'
{% endhighlight %}

Instead of using the exact words as the keys of index , we use their metaphones , to qoute from the original post that inspired to do this :

...Enter phonetic algorithms, a blessing to the linguistically challenged.

Phonetic algorithms are actually pretty simple. They are just a way of taking a word, dropping out some letters (according to a set of rules), and providing you with a rough representation of how that word sounds. Common phonetic algorithms are Soundex and Metaphone. The former is pretty dated now, but we understand the latter to be more up-to-scratch (any to play nicely with other Western languages)...



Looking for a Metaphone implementation in ruby , I come across [metaphone.rb](http://text.rubyforge.org/svn/lib/text/metaphone.rb)

I planned to build the index every thirty minutes for now .Depending on the use of search and interval new posts are created , we can fine tune that.If there were frequent searches and less frequent new posts , we could update the search index everytime a new post is made.

But for now , we will have a rake task that will run once in every thirty minutes,


{% highlight ruby %}
namespace :search do
  desc 'Build index'
  task :build_index  => :environment do 

    require './lib/metaphone.rb'
    $r = Redis.new unless $r

    last_indexed_time = $r.get('last_indexed_time')
    if last_indexed_time.nil? 
      #Never been indexed
      last_indexed_time = Post.order(:updated_at).select(:updated_at).first.updated_at
    end

    puts "Indexing from #{last_indexed_time}"

    Post.where('updated_at > ?',last_indexed_time).each do |p|
      Text::Metaphone.metaphone(p.content).split(' ').each do |mtphn|
        $r.sadd "word:#{mtphn}",p.id
      end
    end  

    last_indexed_time = Time.now
    $r.set('last_indexed_time',last_indexed_time)

  end
end

{% endhighlight %}

We will have an index build by now , time to query the index :


{% highlight ruby %}
class Search
  require 'metaphone.rb'

  def initialize(query)
    $r = Redis.new unless $r
    @query = query
  end

  def results
    index_keys = Text::Metaphone.metaphone(@query).gsub(/(\w+)/,'word:\1').split
    $r.sinter(*index_keys)
  end

end

{% endhighlight %}

Now we can perform the search as 


{% highlight ruby %}
Search.new('arest').results
{% endhighlight %}

which would give the result '2'.
