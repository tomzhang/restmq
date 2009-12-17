======
RESTMQ
======

Redis based message queue.
--------------------------

:Info: See `my blog <http://zenmachine.wordpress.com>`_ for more information.
:Author: Gleicon Moraes <gleicon@gmail.com>
:Author: Alexandre Fiori


About
=====
RestMQ is a message queue which uses HTTP as transport, JSON to format a minimalist protocol and is organized as REST 
resources. It stands on the shoulder of giants, built over Python, Twisted, Cyclone (a Tornado implementation over twisted) and Redis.

Redis is more than just a key/value db, and its data types provided support for this project.

The queues are created on the fly, as a message is sent to them. They are simple to use as a curl request can be.



Example
========
A http client (curl) post to /queue:

Point your browser to http://localhost:8888/c/test

Run $ curl -X POST -d "queue=test&value=foobar" http://localhost:8888/ 

Your browser is acting as a consumer to the queue. Using json encoded data it's easy to fit the data into a js based app.

Aside from the COMET consumer, there are xmlrpc methods, rest routes and the JSON protocol to manipulate queue items.


JSON Protocol
=============
::

    {
        "cmd": "add",
        "queue": "genesis",
        "value": "abacab"
    }

Creates the queue named *"genesis"* and inserts the string *"abacab"* as the message.

If we want to take that out of the queue, the payload would be like that:

::

    {
        "cmd": "take",
        "queue": "genesis"
    }


The message can be formatted as a json object, so more complex data can be sent.
It really mimics some of `Amazon SQS <http://aws.amazon.com/sqs/>`_ workings, because it's a simple queue.

For the first release it has:

- Select, EPoll or KQueue concurrency (depends on twisted)
- Persistent queueing using Redis
- Can work on pools, N daemons consuming from the same queues.
- Cute ?
- Small codebase


Dependencies
============
- python, twisted
- `redis client <http://github.com/fiorix/txredisapi>`_: 
  git clone git://github.com/fiorix/txredisapi.git
- `cyclone <http://github.com/fiorix/tornado>`_: 
  git clone git://github.com/fiorix/tornado.git 


Running
=======
Development environment:

::

    twistd -ny restmq_server.tac

Production environment (needs epoll and proper user/group): 

::

    twistd --pidfile=/var/run/restmq.pid --logfile=/var/log/restmq.log \
           --reactor=epoll --uid=www-data --gid=www-data --python=restmq_server.tac


Tests
=====

::

    examples/test_rest.sh
    examples/test_xmlrpc.py
    python examples/test_comet.py
    python examples/twitter_trends.py
    python examples/test_comet_curl.py  
    python restmq_engine.py -h


Files
=====
- restmq/dispatch.py: - a simple command dispatcher
- restmq_engine.py: the redis abstraction layer to the queue algorithm
- restmq_server.tac - the main app (a web service)


Credits
=======
Thanks to (in no particular order):
    Salvatore Sanfilippo for redis and for NoSQL patterns discussion.
    Alexandre Fiori for the redis client enhancement and patches.