# using CLI



#### Querying sub-objects

##### a) dot notation

```
$ ./equal.run \
--get=model_collect \
--entity=core\\User \
--fields=[id,name,groups_ids.name,groups_ids.description]
```

##### b) array notation

```
$ ./equal.run \
--get=model_collect \
--entity=core\\User \
--fields="{id,name,groups_ids:{name,description}}" 
```

​	