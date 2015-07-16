# php-redis #

php-redis is KeyValue data development framework, which allows to easily implement data logic on top of Redis - powerful and fast KeyValue DB. The idea is to provide platform for building data model on top of it.

Right now framework consists of such elements:
  * Connection pool
  * "Entity", "List" and "Set" basic classes
  * Redis API (not supposed to be used directly)

## Quick start ##

1. Install Redis 1.0 or later: http://code.google.com/p/redis/
```
tar -xvf redis-1.0.tar.gz
cd redis-1.0
make
./redis-server redis.conf
```

2. Download latest php-redis platform from here: http://code.google.com/p/php-redis/downloads/list

3. Write some code:

```

<?php

require 'redis.php';
require 'redis_pool.php';
require 'redis_peer.php';

class note extends redis_peer
{}

$note = new note();

# Create note, primary key is generated automatically
$id = $note->insert( array('title' => 'Hello', 'body' => 'world!') );

# Update note
$id = $note->update( $id, array('body' => 'wwwwworld!') );

# Get some note by primary key
$note_data = $note->get_by_id( $id );

# Delete note
$note->delete( $id );

```

# Basics #

Read the [basics](http://code.google.com/p/php-redis/wiki/Basics) to get deeper look

# Benchmarks #

Take a look at [benchmarks](http://code.google.com/p/php-redis/wiki/Benchmarks)

# Contribute #

For those who are interested in contributing the project, you are welcome, let me know.