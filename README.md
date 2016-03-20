# Using sp_change_users_login to fix SQL Server orphaned users #

With any server migration, ideally you want things to run smoothly and re-creating SQL Server logins from scratch is not something you really want to or should be doing.

So first I’ll talk about the ways in which you can transfer logins from one SQL Server to another and explain about the SID’s further down the post.

## Ways to transfer SQL logins ##

Now you can do this the easy way or the hard way!

You can manually set up copies of your logins on your new server with passwords (if you know them) or you can automate this task with the help of either a T-SQL script or SSIS.

I don’t know about you but I don’t like manual and I would recommend that you automate this each time.

## Using T-SQL to transfer logins between SQL Server instances ##

You can obtain a script from Microsoft [here](https://support.microsoft.com/en-us/kb/918992). You run the script, it creates a stored procedure called sp_help_revlogin on your SQL Server. When you run the stored procedure, it outputs CREATE LOGIN statements for all your server logins including their passwords and “sids”.

You can then take that script, run it on your new SQL Server to set up the server logins there. Obviously taking care to look over it before you execute it to make sure that it has captured everything and to ensure that you are not copying over anything that you do not need on your new instance.

The script will set up the logins on your new server in using the default language configured for that server so make sure that is configured correctly first.

## So what is a SID? ##

SID is short for “security identifier” A SID is an internal id which gets assigned to a server login when the login is created.

The SID can be viewed by querying the sys.server_principals system view or you can also view it by looking at sys.syslogins

Unless you create your logins on your new server using a method such as the sp_help_revlogin or the SSIS transfer logins task with “CopySids” enabled, your logins will be created with new SIDs.

I am not sure whether it is possible that your new server will ever assign the same SID but I do know that it is very unlikely. Perhaps you have more chance of winning the lottery jackpot

## So what problems does this cause? ##

As the SIDs are different, when you come to copy across/restore your databases from your old server, the database logins simply won’t work as they will be orphaned from their original SID.

You may encounter this error for example if you try and add a server login to a database which you have just copied across:

    **Error 15023: User already exists in current database**

This will then need to be corrected.

There is a procedure that you can run in order to do this and it basically takes the server login and database login and marries them up by name with the same SID.

This procedure is called **sp_change_users_login** and I’m going to explain below.

## sp_change_users_login to the rescue! ##

Firstly, there may be a number of orphaned users, so the best thing to do is run **Orphan_Checks.sql** inside each database you are checking

If you already have a login which you want to map your database user to, you could run Orphan_Update.sql (note that the first instance of ‘Username1’ is the user in the database, the second instance is the login to be mapped to)