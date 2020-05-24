1. you can use redis as a primary database or as cachier, you can interact with redis
   for reading and writing and interact with primary db only if the data is not in 
   redis.

2. To run redis, cd to the folder of redis and: 

      (it is like mongod)
      src/redis-server

3. to use it in another window

      (it is like mongo)
      src/redis-cli