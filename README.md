Problem-I
Solution-Summary

For this exercise , I have choosen a tool called Hackolade (https://hackolade.com/usecases.html) for Data Modelling/Schema Design. This tool is defnitely having an upper edge when compared with different tools from the market place when it comes to Data modelling for MongoDB database. 

Coming to the ER Diagrams I have attached the following with the zip file. 

1.Embedded_ER_Diagram_Gamer_Profile.jpg
This image consists of Attributes and their corresponding Datatypes. This diagram will have an embedded document for Employer details. 

2.Embedded_ER_Diagram_Gamer_Profile_Schema_Design.jpg

This image consists of Schema defnition of different attributes present in the GamerProfile collection with Employer details embedded imside the main document. 

3.Reference_ER_Diagram_GamerProfile_EmployerDetails.jpg
This image consists of now two collections ( GamerProfile and EmployerDetails) where _id attribute from EmployerDetails collection is a primary key and is related/refernced to GamerProfile collection via Employers array using _id as foreign key. More details in sample collection data.

4.Reference_ER_Diagram_EmployerDetails_Schema.jpg
This image consists of Schema defnition of different attributes present in the EmployerDetails collection. 

5.Reference_ER_Diagram_GamerProfile_Schema.jpg
This image consists of Schema defnition of different attributes present in the GamerProfile collection. 

Embedded Document Sample collection 
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

Sample documemt for Reference pattern

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


Update the gaming interest of the user with email mquinn@efuse.io


Find every user with the employer name eFuse
----Good--------
db.GamerProfile.createIndex({"Employers.employername":1)
dn.GamerProfile.find({"Employers.employername": "eFuse"})
-------Good---------------

db.GamerProfile.createIndex({"email":1},{unique:true})
db.GamerProfile.updateOne(
   { "email": "mquinn@efuse.io" },
   { $addToSet: { gamigInterest: "FortNite" } }
)


-------Good-----
db.GamerProfile.createIndex({"Employers":1})
db.GamerProfile.deleteMany({ Employers: {$elemMatch : { jobtitle:"Data Analyst",date:ISODate("2020-01-28")}}})
-----Good-----





Your selection should minimize the result set so that it will be benefecialfor the database performance
