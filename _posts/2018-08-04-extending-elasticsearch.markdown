---
layout: post
title: Extending Elasticsearch
date: 2018-08-04 13:37
---

Elasticsearch has a whole bunch of analyzers, tokenizers and filters build in wich can be used to manipulate incomming character streams either for indexing or searching. But it might happen that something you want cannot be composed with the default tools provided. This is where plugin extension comes into play.

First thing that needs to be done is to extend the Plugin class. Since we want to add a custom filter we also need to implement the AnalysisPlugin interface and override the getTokensFilter method and add our custom filter.

```java
public class RemoveTokenPlugin extends Plugin implements AnalysisPlugin {
    @Override
    public Map<String, AnalysisModule.AnalysisProvider<TokenFilterFactory>> getTokenFilters() {
        Map<String, AnalysisModule.AnalysisProvider<TokenFilterFactory>> extra = new LinkedHashMap<>();
        extra.put("remove-first-token", FilterFactory::new);
        return extra;
    }
}
```
Next we’re gonna build our custom filter. We’re extending the FilteringTokenFilter interface and implement the accept method. This method tells the caller to in- or exclude the current token in the stream.
```java
public class RemoveFirstTokenFilter extends FilteringTokenFilter {

    private int count = 0;

    public RemoveFirstTokenFilter(TokenStream in) {
        super(in);
    }

    /**
     * This method decides if the current token will be returned to the client.
     */
    @Override
    protected boolean accept() throws IOException {
        // This is a real dumb implementation
        // It will return all but the first token.
        return 0 != this.count++;
    }
}
```
Finally (since it’s Java) a Factory has to be created.
```java
public class FilterFactory extends AbstractTokenFilterFactory {

    public FilterFactory(IndexSettings indexSettings,
                         Environment environment,
                         String name,
                         Settings settings) {
        super(indexSettings, name, settings);
    }

    @Override
    public TokenStream create(TokenStream tokenStream) {
        return new RemoveFirstTokenFilter(tokenStream);
    }
}
```
## Building the shizzle
Elasticsearch has some restrictions on how a plugin must be organized. More on that can be found here. I’m using gradle to build and package my plugin, have a look here.

## Installing and testing the plugin
Installing the plugin is as simple as calling the following command on all nodes in the cluster. All nodes need to be restarted before the plugin gets picked up.

```bash
bin/elasticsearch-plugin install file:///path/to/plugin.zip
```

Now the plugin can be used. In the following snippet I’m creating a filter called drop-first and for the type I’m using the just installed plugin. After that I’m creating a simple analyzer which uses the filter.

```json
PUT local-test-index/_settings
{
  "settings": {
    "index": {
      "analysis": {
        "filter": {
          "drop-first": {
            "type": "remove-first-token"
          }
        },
        "analyzer": {
          "remove-analyzer": {
            "type": "custom",
            "tokenizer": "standard",
            "filter": [
              "drop-first",
              "lowercase"
            ]
          }
        }
      }
    }
  }
}
```
After adding the settings to the index, I can try the analyzer by using the _analyze endpoint and either use the filter directly, like so
```json
POST local-test-index/_analyze
{
  "tokenizer" : "standard",
  "filter" : ["remove-first-token"],
  "text" : "hallo dit is een test" 
}
```
Or use the analyzer:
```json
POST local-test-index/_analyze
{
  "analyzer": "remove-analyzer", 
  "text" : "hallo dit is een test" 
}
```
In both cases the response will look like this.
```json
{
  "tokens": [
    {
      "token": "dit",
      "start_offset": 6,
      "end_offset": 9,
      "type": "<ALPHANUM>",
      "position": 1
    },
    {
      "token": "is",
      "start_offset": 10,
      "end_offset": 12,
      "type": "<ALPHANUM>",
      "position": 2
    },
    {
      "token": "een",
      "start_offset": 13,
      "end_offset": 16,
      "type": "<ALPHANUM>",
      "position": 3
    },
    {
      "token": "test",
      "start_offset": 17,
      "end_offset": 21,
      "type": "<ALPHANUM>",
      "position": 4
    }
  ]
}
```
And as you can see, the first token hallo is ommited.
At this point all pieces are in place to write a decend plugin for elasticsearch.

Have fun with it!