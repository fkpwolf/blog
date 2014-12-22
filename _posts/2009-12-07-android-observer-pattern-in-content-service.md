---
title: Android Observer Pattern in Content Service
author: Fan Fan
layout: post
permalink: /2009/12/07/android-observer-pattern-in-content-service/
dsq_thread_id:
  - 607160663
categories:
  - Mobile
---
This article used *NotePad *project as example. It can be found *sample *dir at Android SDK.

###### 1.Register process. There are 2 repository: a) content service, b) ArrayList in Observable.

a). in NotePadProvider, after we finished querying, there is:

`c.setNotificationUri(getContext().getContentResolver(), uri);`

which in turn invokes (c is type of AbstractCursor)

`mContentResolver.registerContentObserver(mNotifyUri, true, mSelfObserver);`

this type of observer is identified by Uri of resource and maintained by content service which is more like a *event broker*.

b). in init() method of CursorAdapter code, there is

c.registerContentObserver(mChangeObserver);

This type of observer is maintained by corresponding cursor.

###### 2. notify process. These is a little more complex. Because content service will notify Observable first, then Observable will notify cursor adapter. I prepared a sequence to present this.

<img class="alignnone size-full wp-image-512" title="content-service" src="http://fkpwolf.net/WordPress/wp-content/uploads/2009/12/content-service.png" alt="content-service" width="693" height="326" />

From the graph, it&#8217;s found the framework used a lot of inner class to implement call-back functionality. (This was full documented at *Thinking in Java*) This made the code a little hard to read.

So use this approach, we can register a observer on *Contacts.People.CONTENT_UR*I, then when the contacts was changed, we will get a notification, as the example [here][1]. It is simple but remember to *unregisterContentObservers *your observer. You had better do it at a service. If we do it at an Activity and register at onCreate and un-register at onStop, when the activity went into background, it will not get notified.

So there are 2 level of observer. These 2 levels all can be found at OSGi. It make the framework more flexibility.

 [1]: http://mylifewithandroid.blogspot.com/2008/03/observing-content.html