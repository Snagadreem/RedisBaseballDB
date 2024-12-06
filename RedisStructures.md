1. I am going to adapt stats for players to be used as in-memory key-value pairs, this will include stat years, each separate statistic, and the player it corresponds to.
2. For each statistic and year, for example, pitcher wins in 2015, there will be a sorted set to which you can add a score value and the player id of the corresponding player. So each player's statistics will be stored in separate sorted sets along with every other player who has statistics of that type in that year. In this case, the key for each sorted set would be 
```"stat|<playerType>|<statName>|<year>"``` 
with the member being the player id and the score being the stat value. This not only keeps track of player statistics, but also keeps a ranking which can be useful for queries.
3. CRUD operations
    - Create: ```ZADD "stat|pitcher|wins|2015" 5 "1"  # Score: 5 (wins), Member: "1" (player ID)```
    - Read:
        - Example: Top 3 pitchers by wins in 2015
        ```ZRANGE "stat|pitcher|wins|2015" 0 2 REV WITHSCORES```
        - Example: Get the stat value for a specific player
        ```ZSCORE "stat|pitcher|wins|2015" "1"  # Player ID: "1"```
    - Update:
        - Example: Replacing an old stat value with a new one
        ```ZADD "stat|pitcher|wins|2015" 7 "1"```
        - Example: Incrementing an old stat value by a specified amount (in this case, 1), useful for a stat like games played, where you can just increment it by 1 for everyone who played a game that day
        ```ZINCRBY "stat|pitcher|wins|2015" 1 "1"```
    - Delete:
        - Remove a stat in the set: ```ZREM "stat|pitcher|wins|2015" "1"```
        - Delete the entire stat set: ```DEL "stat|pitcher|wins|2015"```

This is applicable to all statistics and years, just by creating a new sorted set with new values.
