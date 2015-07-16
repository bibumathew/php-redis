Data platform consists of 3 data classes:

  * Entity peer (redis\_peer)
  * List peer (redis\_list\_peer)
  * Set peer (redis\_set\_peer)

## Entity peer ##

Entity peer is used to work with data objects. You will need this in most cases. This peer is providing the basic CRUD operations for operating with business logic entities.

This example provides implementation for authentication system based on KeyValue DB. It saves entity data under primary key, as well as entity ID under its nickname in order to be able to get entity by its nickname later:

```
<?php
class user_peer extends redis_peer
{
	public function insert( $data )
	{
		$data['ts'] = time();
		$id = parent::insert($data);
		$this->get_connection()->set($this->name_space . 'nickname' . $data['nickname'], $id);
		return $id;
	}

	public function get_by_nickname( $nickname )
	{
		if ( $id = $this->get_connection()->get($this->name_space . 'nickname' . $nickname) )
		{
			return $this->get_by_id($id);
		}
	}
}

$data = array('nickname' => 'test', 'password' =  '12345');

$u = new user_peer();

if ( $u->get_by_nickname($data['nickname']) )
{
	die('Choose another nickname');
}

$id = $u->insert( $data );
echo 'Your ID is ' . $id;
?>
```

## List peer ##

Though KeyValue DB does not allows you to do the range requests (to get list of items based on some condition) php-redis platform provides you with tools for list operations.

List peers provides such methods for list functionality: inserting an item, deleting items, retrieving lists (with limits and offsets), retrieving lists with filters (experimental), truncating lists.

```
<?php

class messages_list_peer extends redis_list_peer
{
	public function insert( $user_id, $message_id )
	{
		parent::insert($user_id, array('id' => $message_id), false);
	}

	public function delete( $user_id, $message_id )
	{
		parent::delete($user_id, array('id' => $message_id));
	}
}

# Message ID is supposed to be loaded from another peer entity (message_peer)
$message_id = 1

# Some user ID
$user_id = 1

$m = new messages_list_peer();
$m->insert($user_id, $message_id);

$user_messages = $m->get_list($user_id);
?>
```

## Set peer ##

There is a group of tasks when you need just partial list functionality (you don't care about items order but you care about items presence or absence in the list). Say you are implementing something like twitter followers list. You don't need to know the order of your followers - this is useless, you just need to know all of them, and be able to check if some user is in this list or not.

For this purposes Redis provides you with sets. To use sets you must use redis\_set\_peer class. The example with followers stuff:

```
<?php

class user_followers_peer extends redis_set_peer
{
	public function is_follower( $user_id, $follower_id )
	{
		parent::is_member($user_id, $follower_id);
	}
}

$user_id = 1;
$follower_id = 2;

$f = new user_followers_peer();

# Add follower
$f->add($user_id, $follower_id);

# Checking if user is followed
$f->is_follower($user_id, $follower_id); // returns true
$f->is_follower($user_id, 123); // returns false

?>
```