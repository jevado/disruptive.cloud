---
layout: post
title:  "Empty strings in Json objects within DynamoDB"
date:   2017-07-20 19:00:00 +0200
tags:
    - aws
    - dynamodb
    - json
---

The enthusiasm to start experimenting and blogging again started a few weeks ago when I was studying for my AWS Solution Architect exam. Luckily I passed, man I was nervous, and decided fairly soon after passing the test that I wanted to get the Developer and DevOps certificates as well. 

With studying for these certificates comes experimenting as well and today I bumped into a rather peculiar issue with loading a json document into a dynamo. The error: *An AttributeValue may not contain an empty string*. As it turns out, by design empty strings are not allowed as values.

Apparently a fix for this is an [outstanding request][aws-forum] since 2012, which makes me wonder whether it’s incredibly hard to fix or if there is an huge list of other feature requests with more added value that keeps the dynamodb team busy.

I decided to search the internet for a solution and I bumped into a [recursive function on stackoverflow][initial-solution], unfortunately that specific function doesn’t have the desired result for all use cases, so I decided to write something myself.

The requirements are simple, all empty string key value pairs should be removed from the object and the object can be a collection of objects or a single object with practically unlimited nesting (as far as the function is concerned ). An object could look like this:

{% highlight json %}

[{
    "a": "b",
    "c": ["d", "e", "f"],
    "g": {
      "h": "i",
      "j": "k"
    },
    "l": [{
        "m": "n"
      },
      {
        "o": "p",
        "q": "" 
      }
    ]
  },
  {
    "r": "s",
    "t": ""
  }
]

{% endhighlight %}

Inspired by the recursive function, but with very rusty recursive skills, I came up with the following which seems to work for all use cases so far.

{% highlight ruby %}

    def strip_empty_key_value_pairs_from_json_object(json)
      if json.is_a?(Hash)
        result = {}
        json.each do |key, value| 
          result[key] = strip_empty_key_value_pairs_from_object(value) unless value == ""
        end
      elsif json.is_a?(Array)
        result = []
        json.each do |value|
          result << strip_empty_key_value_pairs_from_object(value)
        end
      else # string, return the passed value back as result
        result = json
      end
      result 
    end
  end


{% endhighlight %}

When using the method like this is should remove both the **q** and **t** key value pairs

{% highlight ruby %}           
  require 'json'
  result = JSON.parse(some_result_from_a_json_api)
  result = strip_empty_key_value_pairs_from_object(result)
{% endhighlight %}

Hope it's useful. If you have any comments please let me know. You can mail me for now, but I intend to enable disqus or something similar somewhere in the next few weeks. 

[aws-forum]: https://forums.aws.amazon.com/thread.jspa?threadID=90137
[initial-solution]: https://stackoverflow.com/questions/30427610/in-ruby-recursively-remove-blank-items-from-json-string

