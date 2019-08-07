---
layout: post
title: Updating documents with nested fields
date: 2019-05-30 13:37
---

The other day I needed to add an additional field to all documents in one of our indexes.
The documents look something like this

```java
{
  "field1": "foo",
  "field2": "bar",
  "nested": [
    {
      "field1": "foo",
      "field2": "bar",
    },
    ...
  ]
}
```
I needed to add an extra field to all the nested fields so the normal partial update does not work here. I had to put on my painless cap for this one. This is what I dreamed up.

```
POST test-index/_update_by_query
{
  "script": {
    "source": """
    for (int i=0; i<ctx._source.nested.size();i++) {
      ctx._source.nested[i].test = "blaa" 
    }
    """,
    "lang": "painless"
  },
  "query": {
    "match_all": {}
  }
}
```
