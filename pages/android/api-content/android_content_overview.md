---
title: Android SDK - Content SDK Overview
keywords: android, content, getting started, halo, instance, module, query, search, sync
last_updated: November 14, 2016
tags: [content]
sidebar: android_sidebar
permalink: android_content_overview.html
folder: android
---

[![Download](https://api.bintray.com/packages/halo-mobgen/maven/HALO-Content/images/download.svg) ](https://bintray.com/halo-mobgen/maven/HALO-Content/_latestVersion)

## Add dependency

In the HALO plugin add the following to enable the content sdk.

```java
apply plugin: 'halo'

halo {
	...
	services {
		content true
	}
	...
}
```

## Content API methods

The content API is the facade for the Content HALO SDK. Importing this library will need a valid HALO instance configured with some credentials.
The HALO Content SDK allows the user to retrieve instances from the HALO Backend in two main ways:

* Search
* Sync

{% include tip.html content="Use the search method to get conent when you want to get some concrete elements, segmented data or certain information." %}
{% include tip.html content="Use the sync method if you prefer to download the whole module to use if offline. It is better for performance than search." %}

Creating an instance of the Content API is really simple once you have your HALO running. Just write the following line:

```java
HaloContentApi contentApi = HaloContentApi.with(halo);
```

In addition you can provide a default locale to include it in all the queries even if you didn't provide it in the search/sync.

### Search
If you want to bring certain data based on some criteria or search some of them in the HALO Backed, you have to use the search operation in the API. To do that we provide a class called ```SearchQuery``` with a simple fluent API that allows you to specify the criterias for the search. Once selected, you can perform the search and cache the result in local for offline use. See the example above:

```java
SearchQuery query = SearchQuery.builder()
	.moduleIds("myModuleId")
	.beginSearch()
		.eq("name", "Sample")
	.end()
	.build();
contentApi.search(Data.NETWORK_ONLY, query)
	.asContent()
	.execute(callback);
```

This search will request all the instances for the module id "myModuleId" and which body contains a name with the value "Sample". Check out the rest of the available options in [the detailed documentation](./android_content_detailed_api.html).

### Sync
The sync operation is thought for performance critical tasks. It allows to synchronize a full module consuming the less amount of data possible.

Syncing involves three steps:

* Listening for sync updates.
* Requesting a sync for a module.
* Requesting synced local instances.

Check out the following example for a full sync lifecycle:

**Listen for sync updates**

```java
//Start listening for updates
ISubscription subscription = contentApi.subscribeToSync("my module name", listener);
//When you are done or to free memory
subscription.unsubscribe();
```

**Request a sync for a module**

```java
//Create the sync query
SyncQuery query = SyncQuery.create("my module name", Threading.POOL_QUEUE_POLICY);
contentApi.sync(query);
```

**Request the synced instances**

```java
//Request the synced content
contentApi.getSyncInstances("my module name")
	.asContent(MyCustomClass.class)
	.execute(callback);
```

{% include note.html content="Make sure your MyCustomClass.class is properly annotated with LoganSquare ```@JsonObject``` annotation to make it work properly, otherwise the result will not be parsed. You can check it in [content parsing section](./android_content_detailed_api.html#content-parsing)." %}

If you want to go in deep into this module, please refer to [the detailed documentation](./android_content_detailed_api.html).