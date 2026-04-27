1. Set a key user:1:name with value "Alice" . Then retrieve it.

```
hset user:1 name "Alice"
hget user: 1
```

---

2. Set a key user: 1: loginCount with value o. Increment it by 1 using a Redis command (without manually reading and writing). Run the increment 3 more times. Then retrieve the final value.

```
hset user:1 loginCount 0
hincrby user:1 loginCount 1
hincrby user:1 loginCount 3
hget user:1 loginCount
```

---

3. • Set a session key session:abc123 with value "user_data_here" that automatically expires in 30 minutes.

```
set session:abc123 "user_data_here" ex 1800
```

---

4. Check how many seconds remain on the key no longer has an expiry.session:abc123 . Then remove the expiry so it becomes permanent

```
persist session:abc123
```

---

5. • Check whether the key user:1:email exists. Then delete user:1:name and verify it no longer exists.

```
exists user:1:email
del user:1:name
exists user:1:name
```

---

6. What does a TTL of -1 mean? What does -2 mean? Demonstrate both cases with actual keys.

```
# -1, key have no exp
ttl session:abc123

# -2, key does not exists
ttl no_key
```

---

7. You are building a notification queue. Push the following messages to a list called notifications:userl - push "You have a new message" to the left, then "Your order has shipped" and "Password changed successfully" to the right.

```
lpush notifications:user1 "You have a new message"
rpush notifications:user1 "Your order has shipped"
rpush notifications:user1 "Password changed successfully"
```

---

8. Retrieve all elements of notifications:userl without removing them.

```
lrange notifications:user1 0 -1
```

---

9. Add 5 messages to a list called taskQueue. Then retrieve only the first 3 tasks without removing them.

```
rpush taskQueue "task1" "task2" "task3" "task4" "task5"
lrange taskQueue 0 2
```

---

10. Store a user profile for user ID 201 under the key user:201 with these fields:

- name: "Bob"
- email: "bob@example.com"
- age: "34"
- role: "developer"

```
hset user:201 name "Bob" email "bob@example.com" age "34" role "developer"
```

---

11. Retrieve only the email field of user:201.

```
hget user:201 email
```

---

12. • Retrieve all fields and values of user:201

```
hgetall user:201
```

---

13. Check whether the field phone exists in user: 201. Then add it with value "9876543210" and verify it was added.

```
hexists user:201 phone
hset user:201 phone 9876543210
hexists user:201 phone
```

---

14. Add the following users to a set online:users: "alice", "bob", "carol", "alice". Retrieve all members. Ho many are stored?

```
sadd online:users "alice" "bob" "carol" "alice"
smembers online:users
```

---

15. Remove "bob" from the set. Confirm the count dropped by 1.

```
srem online:users "bob"
```

---

16. You have two sets: page:home:viewers with {"alice", "bob", "carol"} and
    page:about:viewers with {"carol", "david", "eve"]. Find the users who viewed both pages (intersection).

```
sadd page:home:viewers "alice" "bob" "carol"
sadd page:about:viewers "carol" "david" "eve"
sinter page:home:viewers page:about:viewers
```

---

17. Build a game leaderboard called leaderboard:game1 . Add these players with their scores:

- "alice": 1500
- "bob": 2300
- "carol": 800
- "david": 3100
- "eve" : 1900

```
zadd leaderboard 1500 "alice"
zadd leaderboard 2300 "bob"
zadd leaderboard 800 "carol"
zadd leaderboard 3100 "david"
zadd leaderboard 1900 "eve"

zrange leaderboard 0 -1 withscores
```

---

18. Display the full leaderboard from highest to lowest score, showing scores alongside names.

```
ZREVRANGE leaderboard 0 -1 WITHSCORES
```

---

19. Explore commands SCAN & OBJECT IDLETIME

```
# SCAN -> to incremently get all keys, without blocking the entire db, like keys command does
# OBJECT IDLETIME -> time between now and last access of the key
```

---
