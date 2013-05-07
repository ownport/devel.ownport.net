Title: Модель вычислений на базе акторов (видео)
Author: d.rey
Date: 2013-01-10 14:50:00
Slug: blog-post
Tags: video,actor,development,lecture,parallel,computing

Карл Хьюитт объясняет суть модели вычислений на базе акторов.

<iframe style="height: 320px;width: 600px" src="http://channel9.msdn.com/Shows/Going+Deep/Hewitt-Meijer-and-Szyperski-The-Actor-Model-everything-you-wanted-to-know-but-were-afraid-to-ask/player?w=512&h=288" frameborder="0" scrolling="no"></iframe>
Источник: [letitcrash.com](http://letitcrash.com/post/20964174345/carl-hewitt-explains-the-essence-of-the-actor)

**Notes:**

Actor is fundamental primitive unit of computation. Actor has three essential elements of computation. It has in body: processing, storage, communication.

One end is no end. One actor is no actor. Everything is actor.

One actor can send a message to another actor. Actor has a mailbox. When actor receives a message it can do:

- create more actors
- send messages to actors it knows
- designate what to do with the next message

"Conceptually one message at one time."

Future (special actor)

It can be one address for whole bunch of actors. And one address can have many addresses. Address is not equal to identity. Address like a capability.

A message should be delivered at most one time.

Actor use state based approach.

Links:

- [Inconsistency Robustness 2011](http://www.robust11.org/)
- [Actor Model of Computation: Scalable Robust Information Systems](https://docs.google.com/file/d/0Bykigp0x1j92M0p6b0ZWWE9SS3Frb3loV3NKX2sxdw/edit)
