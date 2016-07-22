---
layout: post
title: AMQP and RabbitMQ
---

AMQP and RabbitMQ

```
# refer : http://www.rabbitmq.com/resources/erlang-exchange-talk-final/ex.html
# The entire credit belong to above link & its author.

# NOTE - 5 components of AMQP
# Producer, eXchange, Channel, queue & Consumer

# NOTE - @ client side
# producer <-> channel <-> connection
# consumer <-> channel <-> connection

# NOTE - in-between client & server
# connection <-> connection

# NOTE - @ server side
# connection <-> channel -> eXchange -> queue
#                 ^________________________|

# NOTE - @ server side - refined
# socket -> demux -> command assembler -> channel -> eXchange -> queue
#  ^___ <-  mux   <- framer <- ___________| ^______________________|
#

# NOTE - RabbitMQ - an AMQP implementation on Erlang/OTP
# NOTE - several extensions of RabbitMQ - http, stomp, xmpp
# NOTE - look for the json-rpc on RabbitMQ
```
