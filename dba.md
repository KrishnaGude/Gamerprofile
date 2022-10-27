Problem-1-Schema Design - Embedded vs Referenced Documents
----------------------------------------------------------------------------------------------------------------
Solution-Summary

For this exercise , I have choosen a tool called Hackolade (https://hackolade.com/usecases.html) for Data Modelling/Schema Design. This tool is defnitely having an upper edge when compared with different tools from the market place when it comes to Data modelling for MongoDB database and other databases too. 

Coming to the ER Diagrams I have attached the following with the zip file. 

1.Embedded_ER_Diagram_Gamer_Profile.jpg
------------------------------------------
This image consists of Attributes and their corresponding Datatypes. This diagram will have an embedded document for Employer details. 

2.Embedded_ER_Diagram_Gamer_Profile_Schema_Design.jpg
------------------------------------------------------
This image consists of Schema defnition of different attributes present in the GamerProfile collection with Employer details embedded imside the main document. 

3.Reference_ER_Diagram_GamerProfile_EmployerDetails.jpg
---------------------------------------------------------
This image consists of now two collections ( GamerProfile and EmployerDetails) where _id attribute from EmployerDetails collection is a primary key and is related/refernced to GamerProfile collection via Employers array using _id as foreign key. More details in sample collection data.

4.Reference_ER_Diagram_EmployerDetails_Schema.jpg
-------------------------------------------------
This image consists of Schema defnition of different attributes present in the EmployerDetails collection. 

5.Reference_ER_Diagram_GamerProfile_Schema.jpg
-----------------------------------------------
This image consists of Schema defnition of different attributes present in the GamerProfile collection. 


Embedded Document Sample collection
--------------------------------------
{
  "_id": 12,
  "FirstName": "Mary",
  "LastName":"Quinn",
  "GamigInterest":["Callof Duty","ApexLegends","FortNite","MineCraft"],
  "Email":"mquinn@efuse.io"
  "Employers": [
    {
      "EmployerName": "eFuse",
      "JobTitle": "Designer Architect",
      "StartDate": ISODate("2019-12-10T06:01:17.171Z"),
      "EndDate": ISODate("2020-12-11T05:01:17.171Z")
    },
    {
      "EmployerName": "Google",
      "JobTitle": "Developer",
      "StartDate": ISODate("2017-08-10T06:01:17.171Z"),
      "EndDate": ISODate("2019-11-30T05:01:17.171Z")
    }
  ]
}

Sample documents and the corresponding collections for Reference pattern
---------------------------------------------------------------------------
Just GamerProfile Collection-- Reference document pattern ( Employers can be seen as reference here)
{
  "_id": 12,
  "FirstName": "Mary",
  "LastName": "Quinn",
  "GamingInterest":["Callof Duty","ApexLegends","FortNite","MineCraft"],
  "email" :"mquinn@efuse.io"
  "Employers": [
    1,
    2
  ]
}

Just Employer Details Collection--Reference Document Pattern
{
  "_id": 1,
  "Employername": "eFuse",
  "JobTitle": "Designer Architect",
  "StartDate": ISODate("2019-12-10T06:01:17.171Z"),
  "EndDate": ISODate("2020-12-11T05:01:17.171Z")
}
{
  "_id": 2,
  "EmployerName": "Google",
  "JobTitle": "Developer",
  "StartDate": ISODate("2017-08-10T06:01:17.171Z"),
  "EndDate": ISODate("2019-11-30T05:01:17.171Z")
}

Problem 2: Query/Index Optimization
----------------------------------------------------------------------------------------------------------------------------------------
Summary: For this exercise I have assumed Embedded Schema for the data.

i)Find every user with the employer name eFuse
---------------------------------------------------------------------------

Query:
------
db.GamerProfile.find({"Employers.EmployerName": "eFuse"});

Index Statement
----------------
db.GamerProfile.createIndex({"Employers.EmployerName":1);

ii)Update the gaming interest of the user with email mquinn@efuse.io
----------------------------------------------------------------------

Query
------
db.GamerProfile.updateOne( { "email": "mquinn@efuse.io" },{ $addToSet: { GamigInterest: "GodOfWar" } })

Why $addToSet?

The $addToSet operator adds a value to an array unless the value is already present, in which case $addToSet does nothing to that array.$addToSet only ensures that there are no duplicate items added to the set.

Index statement:
----------------
db.GamerProfile.createIndex({"email":1},{unique:true})

iii)Remove every user with the job title of Data Analyst that started on 01/28/2020
--------------------------------------------------------------------------------------

Query:
--------
db.GamerProfile.deleteMany({ Employers: {$elemMatch : { jobtitle:"Data Analyst",StartDate:ISODate("2020-01-28")}}})

Index creation statement
--------------------------------
db.GamerProfile.createIndex({"Employers":1}) 

Problem 3: Ooops! Null Values
--------------------------------------------------------------------------------------------------------------------------------------

Solution-Summary
----------------
Null Values will have impact on performance. Having Null values will consume diskspace and RAM . Hence it would be benefecial to not have null values as it will slow down the performance . There are two ways to go about this.

1. Identifying existing Null Values and getting rid of them
2. Also not to Insert Null Values in first place for the respective attributes in the future.

Query to identify Null Values in GamingInterest array
------------------------------------------------------
db.GamerProfile.find({"GamingInterest":{$elemMatch:{"$in":[null], "$exists":true}}})

Type check Query
----------------------
db.GamerProfile.find( {GamigInterest : { $type: 10 } } )

The above query matches only documents that contain the GamigInterest field whose value is null; i.e. the value of the item field is of BSON Type Null (type number 10) 

Query to remove Null Values from an array
----------------------------------------------
db.GamerProfile.updateMany({},{$pull:{GamingInterest:null}});

https://www.mongodb.com/docs/manual/reference/operator/update/pull/
The $pull operator removes from an existing array all instances of a value or values that match a specified condition.

Query/Code to not insert Null values in first place
----------------------------------------------------
There are multiple ways to get rid of Null values from inserting from various application stacks but for our discussion here ,I am using a Java script example . I am not an coding expert :) 

Scenario-1( user saving the profile from the UI with Null Valuesin GamigInterest array)
-----------------------------------------------------------------------------------------
For exammple our assumption here is the data getting saved in the below format for a particular user from the UI

var gamerProfile = {_id: 12,firstName : "Mary",lastName : "Quinn",gamingInterest : ["call of Duty",null,"God of War",null,""]};
//Filter out null values and empty strings 
gamerProfile.gamingInterest = gamerProfile.gamingInterest.filter(function(game){
   return game;
});

The following will be the end result for GamingInterest array in the database. 

output : ["call of Duty","God of War"]

Scenario-2 ( If the user profile data comes in a csv format)
-----------------------------------------------------------------------------------------------

Lets assume ,we get the  user data in a csv file like below

id,firstname,lastname,gamingInterest
12,Mary,Quinn,"[Call of Duty,null,God of War,]"
13,Jhon,Doe,"[Grand Theft,null,Mission Impossible,]

Now , as we notice above we are getting Null values in te GamingInterest attribute.Now using the code ( attached is the Gamer_Profile.js), we can eliminate the Null values even before inserting. 

The script will convert the csv data into Javascript array of objects ( List of user profiles) and from that objects ,it will look for null/empty values in the GamingInterest arrays and remove the Nulls before loading the data into the database.( Inserting into Database is not provided in the script.

Also I found out this github page which discusses the Null value problem and one of the ways to get rid of them
https://github.com/swetaattri073/KWRIScripts/blob/main/remove_null.js
