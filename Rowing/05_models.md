My experience writing models.

Tried to do them by hand, ran into errors with the SQL chain during lazy initialization.

Workout -> Water Split -> Water Split By Athelete

Found the plugin POJO Generator. Spent ~25 minutes recreating every model and it worked!
Seems like my error was due to not using @Column annotations so the names I was using database side were snake_cased while my java models were camelCased.
---
