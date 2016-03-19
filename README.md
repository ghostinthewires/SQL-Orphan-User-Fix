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
- See more at: http://dbadiaries.com/how-to-transfer-logins-to-another-sql-server-or-instance/#sthash.g98CRp9I.dpuf