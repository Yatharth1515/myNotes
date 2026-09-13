

CAAS(Container as a service)
	Give container to a cloud provider it will run and manage for us

popular CAS tech:- K8S, AWS ECS

Physical server(
EC Virtual Machine 1 (
Container 1 , COntainer 2), 
EC Virtual Machine 2(
Container 1 , COntainer 2)
)

Our responsibility 
privide docker image and 
tell configuration like{
	how many containers, CPU/ RAM limit and Scaling rules
}


Function as a service(FAAS)
All we have to do is write a function .Platform execute it when required
Eg -> AWS lambda
{

when we have task which are short lived and event driven
eg-> when we have a video conversion logic from 4k to 1080p and 720p

now we have to write a function & upload to AWS lambda when video is uploaded in S3,
then it will publish an event which will invoke our lambda function it will convert the video in multiple formats and uplaod it to s3.



}

Complete software provided by user -> SAAS 
eg mail , storage




			**AWS Global InfraStructure**
- Region
- Availbiality
- local zone
- edge location ( Pop point of presence)



**AWS Global InfraStructure (Region)**

 consider it as city where aws has created a cluster of availbiality 


 Mumbai(Region 1 ) Singapore(Region 2)


 Many AWS services are regional and does not automzatically replicates across regions
 Eg- ECS , RDS , Lambda

 Global services :- shared across all regions
 Eg-> IAM , cloudFront(CDN) , Route 53 (DNS), etc

 			

 							Globe
 			IAM , cloudFront(CDN) , Route 53 (DNS)

  Mumbai(Region 1) {s3, EC2, RDS} [AZ1 , AZ2 , AZ3]|| Singapore(Region 2) {s3, EC2, RDS} [AZ1 , AZ2 , AZ3]

  some services which are regional are also zonal , means services created in 1 zonal(AZ) not replicated to other zonal(AZ) in same region
  	eg -> Zonal services :-
  			ECS , EBS, etc.
  Regional services but not zonal , so shared among AZs within a region(aws replicates among AZs within a region)
  	eg -> s3 , dynamoDb, lambda

![[Pasted image 20260830182437.png]]





**why region exists**

latency :- 
	closer = faster 
		if users & server both are in india we can provide low latency service.
		if servers are in diffferent location eg USA , latency increases as data travel time increases

Compliance :-
	countries dont want to share data to other countries
Disaster Isolation :-
	Flood in region 1 will not affect region 2



**AZ - avaibiality zone**
Consider a AZ as building or group of buildings in which physical server are kept.
physicak=l servers are the ones where all aws services iltimately lives
if service is regional then aws automatically replicate it to other AZ physical servers


![[Pasted image 20260830183125.png]]



Generally each region has >= 3 AZs

Reason:-
	Multiple AZ is required to survive single center failure
Note :-
		2 AZ are generally 10-100 kms apart ,close enough to be fast but far enough to survive disaster
		 Each AZs , power source, cooling , network are independent
		 2 AZs within a region are connected via private power cables so that round trip time in single digit ms



**Local Zone**


![[Pasted image 20260830184138.png]]


Region is still mumbai but some servers will be running in kolkata
	so small set of users will get services from services like:-
		ECS (virtial machine), EBS (virtual hardisk)
		But rely on parent region for rest of services like s3, dynamo DB
	Suppose we've a usecase where DB access is not much required or in that case this works 
	but if DB access is frequent than those local servers need frequent access to DB which is present on parent region

Eg of local zone

![[Pasted image 20260830184745.png]]


**Edge location or point of presence (POP)**

services running on edge locations are managed & operated by AWS
we dont deploy our servers there all we can do is to make our services use these edge locations.


But what exactly runs on these edge locations ?
	cloudFront(CDN) - CDN used for caching static data like video , images, etc.
	Route53  - DNS -> resolves DNS rqueries (amazon.com -> 54.x.x.x)

**AWS shield**
	provides protection from DDoS attach, it absorbs n/w flood at edge location itself ,before it reaches our region.



![[Pasted image 20260830185803.png]]


 **Diasster Recovery**
   	eg -> Zonal services :-
  			ECS , EBS, etc.
  Regional services but not zonal , so shared among AZs within a region(aws replicates among AZs within a region)
  	eg -> s3 , dynamoDb, lambda

![[Pasted image 20260830182437.png]]





**why region exists**

latency :- 
	closer = faster 
		if users & server both are in india we can provide low latency service.
		if servers are in diffferent location eg USA , latency increases as data travel time increases

Compliance :-
	countries dont want to share data to other countries
Disaster Isolation :-
	Flood in region 1 will not affect region 2



**AZ - avaibiality zone**
Consider a AZ as building or group of buildings in which physical server are kept.
physicak=l servers are the ones where all aws services iltimately lives
if service is regional then aws automatically replicate it to other AZ physical servers


![[Pasted image 20260830183125.png]]



Generally each region has >= 3 AZs

Reason:-
	Multiple AZ is required to survive single center failure
Note :-
		2 AZ are generally 10-100 kms apart ,close enough to be fast but far enough to survive disaster
		 Each AZs , power source, cooling , network are independent
		 2 AZs within a region are connected via private power cables so that round trip time in single digit ms



**Local Zone**


![[Pasted image 20260830184138.png]]


Region is still mumbai but some servers will be running in kolkata
	so small set of users will get services from services like:-
		ECS (virtial machine), EBS (virtual hardisk)
		But rely on parent region for rest of services like s3, dynamo DB
	Suppose we've a usecase where DB access is not much required or in that case this works 
	but if DB access is frequent than those local servers need frequent access to DB which is present on parent region

Eg of local zone

![[Pasted image 20260830184745.png]]


**Edge location or point of presence (POP)**

services running on edge locations are managed & operated by AWS
we dont deploy our servers there all we can do is to make our services use these edge locations.


But what exactly runs on these edge locations ?
	cloudFront(CDN) - CDN used for caching static data like video , images, etc.
	Route53  - DNS -> resolves DNS rqueries (amazon.com -> 54.x.x.x)

**AWS shield**
	provides protection from DDoS attach, it absorbs n/w flood at edge location itself ,before it reaches our region.



![[Pasted image 20260830185803.png]]


 **Diasster Recovery**
 
