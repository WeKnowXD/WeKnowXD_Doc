
## The Database Incident

#### 28-09-2026:

During the night of September 28, we had finalized the last missing endpoints for **api/login**,  **api/weather** and **/weather**. and went to udate the server.

The process on the server was the following.
- Use SCP(Secire Copy Protocol) to move the new webapp to the server
- The SCP was used to protect the newly incldued .env file for **api/weather**
- Once onto the Server, the entire old webapp was copied as a backup
- Then stopped the service 
  - this can be seen in the [Error Logs](http://68.221.70.31:8000/logs/groups/error/WeKnowXD) error id 1504 as a user tried accessing / during the slight period of time the server was down
* The server was confirmed to be up

After completing this the team went to bed, as API calls tested locally on **localhost** was seen working just fine, and it was incorrectly assumed that everything was as is should be.

#### 29-09-2026

The team is all signed up to Machine Learning, and there for had class tuesday at 12.30pm. Therefore it took us until at least **19:51** to disocver the new errors that was happening.

![[team_discord.png]]

Seeing the error failure 500 immidiatley clued the issue in to be on the server. And the team memember who had discovered the errors went in to the server to find the cause.

It was discovered after exameming the whoknows.db file that it had corrupted/overwritten with nothing most likely on a users pc. Meaning whenever a user tried to login or register to the server, there was never a table that could even be found.

A backup copy that the user who discovered the error kept outside of all the prod and test enviroments was then grabbed and set up to the server. Following that, proper ```curl``` was set up and used to test all the endpoints and it was ensured that the web application was now healthy.

The team member logged off for the night after updating the team discord with the incident.

#### 30/09/2026

At 10 past midnight following, the team member who had discovered the initial error, went in and checked the [Error Logs](http://68.221.70.31:8000/logs/groups/error/WeKnowXD) for good measure. And to their frustration, found the that it had started calling **401 Client Error: Unauthorized**

The team member correctly assumed the error was likely due to the fact that when they had fixed the corrupt file, the old backup didn't contain newer **api/register** calls, and therefore data was missing and users couldn't login succesfully.

The team member went to bed, as the group has 9am meetings physically on wednesday.

#### Some good news

At 7am of the 30th **api/login** calls had stopped for a few hours, we assume this to have been random user traffic that had stopped. And we seemingly had a few succesfull logins. However these are assumed to have been users that had registered on the **29th** post the **500 internal server error**.

Around 9am in the morning of the 30th, the team member logged into the server to further while on the way to the group meeting. Where the following steps was done.

* Copied live db and backup db to a new folder for testing
* Tested diffs in lines of db's
* Diffs where found, some 149 or more.
* DB files were merged into a 3rd seperate, to still preverse the old and new in case of failure.
* The new DB file was put into production
* (if you are wondering at this point if we made a small mistake we did indeed)

The webapp was restarted and we have seen no errors since the 30th September 7AM.

#### The Error

The correct thing to do was to make another copy of the live DB, as during the fix although it was a short span of time. It is possible 1-3 users could've signed up during the merging of the DB's
(an estimated guess by the team based on failure activity.)

This means we have potentially lost a few users data completely. 

## What did we learn?

The point of our incident report is to document errors we have made along the way and how we can best learn from them.

Here is the tl:dr of our errors.

1. The team didn't health check the endpoints on the server on the 28th.
2. The team didn't check the DB health on the 28th
3. The team didn't update the DB on the 28th
4. The team didn't update the DB on the 29th
5. The team failed to use best practice for updating the DB on the 30th

#### How could we have avoided this?

If the team had easy health check endpoint testing, sloppy work around the endpoints for the server wouldn't have been done. This would have led to multiple discovered.

1. The 500 internal server error
   
Meaning the team would catch the corrupted DB file quicker.

2. The 401 Client error: Authentication
   
The team could have caught the old users not persisting if health checks includes af variety of data.

3. The potential lost Data

The team needs a standard of how to handle the DB more carefully, for example with written guides and warnings on either GitHub or the team Discord.


#### Moving forward

The good in this was that because of other standards, such as copying the old web app as a backup before updating the server to use the newest version, saved all the old users data we could have lost. We can however strengthen this.

The team discussed how we could best approach and make a formal statement, either for the website and or to the github repo about our data loss.

The team is also getting set up with postman health check calls to the endpoints, that will notify the team members in case of problems. This will also strengthen our reponse time to errors.

The team is also paying more attention to the [Error Logs](http://68.221.70.31:8000/logs/groups/error/WeKnowXD) more frequently, to ensure that the issues indeed are squashed.









