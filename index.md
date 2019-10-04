# Jacob Champness
**Tech Lead, Senior Software Engineer, Software Systems Architect, XP Coach**

_hello@champness.org \|  +1 720 432 7825_

I've built:
- Go APIs, microservices, and infrastructure management
- Scala systems management infrastructure, web services, and utilities
- JavaScript/HTML5 single-page apps
- Rails apps and APIs
- Cellular network protocol implementations
- Django webapps and server utilities
- JEE and J2EE webapps
- .NET web and desktop apps

I've used:
- Go, Ruby, Python, Scala, Java, JavaScript, Lua and C#.NET
- DynamoDB, AWS Athena, MongoDB, redis, Memcached, PostgreSQL, MySQL,
and Oracle
- Django, Rails, Backbone, Akka, and Spray
- docker, Kubernetes, QEMU, and Vagrant
- Git, mercurial, SVN, CVS and Perforce
- Linux, macOS, OSX, Solaris, HP-UX, and Windows

I practice agile methods; emergent design and evolutionary
architecture; test-first TDD; loose coupling; inversion of control and
dependency injection; and code kata.

#### Experience

##### Technical Leader -- Cisco Systems, San Jose, California (2016-present)

_Tech: Go, Python, REST; AWS S3, Cloudwatch, DynamoDB, Route53,
Athena, Quicksight, IAM, SQS; DNS, SSH; Kubernetes, Docker, QEMU,
Vagrant; Terraform, Ansible, IPMI; Drone, TravisCI, Jenkins;
PagerDuty; OSX, Alpine linux, Windows_

Technical lead for design and implementation of the Billing
platform, a microservices architecture running the billing and usage
reporting functions for a private-cloud platform of
highly-available globally-distributed cloud-computing primitives.  The
central billing service is a REST API implemented in Go running on
Kubernetes clusters of Docker containers in geographically distributed
datacenters with HA latency-based DNS routing for widely dispersed
operators and consumers of platform services.  The service is responsible
for authorizing charges against platform accounts by issuing signed JWT
tokens to authenticated users.  Platform service providers report billable
customer usage to billing service endpoints, which is then aggregated
into account statements using infrastructure running as Lambda
functions in AWS and generating artifacts to S3 with monitoring and
reporting infrastructure implemented in Athena and QuickSight and
monitoring in Cloudwatch with PagerDuty integrations.  I executed the
initial implementation working solo, and then built and led a
geographically-distributed team across Europe and North and Central
America which continues to iterate on the platform and operate it in
production for the past two years.

When I joined the OpenDNS/Umbrella group at Cisco, I first worked on
the devops team responsible for the bare metal fleet spanning 70 DCs 
globally.  It was an ops-heavy team in need of balance on
the dev side, and I was able to pick up a lot of ops expertise from my
colleagues while also sharing with them about design and development
best practices, and test-first TDD.  I built an API for lifecycle
management of QEMU VMs on thousands of blades.  I led the initiative within the
team to build a system in Go to manage the lifecycle of those
bare-metal hosts from initial datacenter uplift to life-cycle
management from paving to patching to decommissioning, from PXE boot
to OS installation to integration with the fleet.

##### VP of Engineering -- Trip30.com, Boulder, Colorado (2013-2016)

_Tech: Go, Ruby, Javascript, Coffeescript, CSS, Sass, Websockets;
Redis, MongoDB, PostgreSQL; Rails, Backbone; Git; IntelliJ, RubyMine,
Goland; Heroku; AWS EC2, Route 53, S3, Lambda; eXtreme Programming_

Designed, staffed, and implemented Trip30, giving travel industry
suppliers insight into the needs of individual travelers while
providing travelers a comprehensive view of the geographical and
temporal aspects of their planned and completed travel. Trip30 is
implemented as a JavaScript, HTML, and HTML5 single-page app written
with CoffeeScript and Backbone as a client of a Rails API running in
Heroku with datastores in PostgreSQL and edis and live  reporting and
analytics functions implemented using Websockets as a client of
microservices built in Go, implementing a REST API with datastores in
PostgreSQL, Redis and MongoDB.


##### Senior Software Engineer -- minuteKEY, Boulder, Colorado (2011-2013)

_Tech: Scala, Akka, Spray; PostgreSQL, MongoDB; AWS EC2; Ubuntu,
CentOS, Windows, Cygwin; Scrum_

As a senior devops software engineer, I built and administered
PCI-compliant infrastructure running on EC2 for payment processing and
remote management of minuteKEY's network of self-service kiosks
deployed in thousands of big box stores across North America. The
management platform is implemented in Scala with Akka and Spray and
datastores in PostgreSQL and MongoDB and is used for remote management
over SSH and VNC of remote kiosks running Cygwin virtual hosts on
Windows PCs which control the UI and the internal camera, and operate
the key-handling robot.


##### Director of Software Development -- Alpine Access U, Denver, Colorado (2009-2011)

_Tech: CentOS, OSX; Python, JavaScript; MySQL; memcached; Django; AWS
EC2, S3; Scrum_

Alpine Access is a home-shoring virtual call center, which recruits
tens of thousands of agents per year through Alpine Access U. I
recruited, hired, and led the engineering team in developing the
Alpine Recruiting Platform, a Django web application running in
multiple EC2 zones with state-sharing across zones via memcached,
supporting complex business processes for recruiting new agents, from
initial contact through completed onboarding; and Elmer, a Django
learning management system running on multiple EC2 zones. This system
delivers training to new agents, tracks their progress through
training, and maintains training records data for tens of thousands of
agents.


##### Senior Software Engineer -- Openwave Systems, Redwood City, California (2006-2009)

_Tech: Ubuntu, Windows, Solaris; Java; Eclipse, IntelliJ; TeamCity,
CruiseControl; git, cvs, Perforce; Scrum_

Designed and implemented core functionality of the SUPL Positioning
Center (SPC), the network element providing assistance data for AGPS
(Assisted GPS) requests in Secure UserPlane Location (SUPL), the Open
Mobility Alliance's standard for location-based services in GSM/UMTS
cellular networks. Implemented RRLP (Radio Resource Location Protocol)
messaging flows between the SPC and the SET (SUPL-enabled Terminal) in
Java on Solaris. Designed and implemented connection-management with
the SET. Designed and implemented core functionality within SUPL.
Designed and implemented core functionality for the V2 interface to
Openwave's Location Manager, implementing Qualcomm's specification for
LBS in CDMA cellular networks. Implemented SMPP (Short Message
Peer-to-Peer protocol) messaging flows. Designed and implemented SMSC
(SMS Center) connection-management.


##### Senior Programmer/Analyst -- Resource Data Incorporated, Anchorage, Alaska (2004-2006)

_Tech: Red Hat, Windows Server; C#.NET, VB.NET, Java; Oracle, SQL
Server; Tomcat, Velocity, Struts, ASP.NET; Eclipse, Visual Studio.NET;
CruiseControl.NET; eXtreme Programming (XP)_

_New Employee Orientation (NEO), Municipality of Anchorage_
Architect and lead developer for NEO, a self-service web front-end to
PeopleSoft for new municipal employees, written in ASP.NET. NEO also
served as a demonstration project for municipal developers, being the
Municipality's first .NET project. A major objective was to establish
solid patterns and best practices for the development of future
Municipal projects. My architecture established a framework for
object-oriented .NET web applications supporting MVC/Model 2
decoupling of data, presentation, and business logic.

_Timesheet Entry and Review System, State of Alaska, Department of
Fish & Game_
Consulted with a cross-departmental client technical team from the
Alaska Department of Transportation and Department of Fish and Game on
implementation of test-driven development methodology for the
Timesheet Entry and Review System.

 _AKFINWeb, Alaska Fisheries Information Network_
 Tech lead for the team developing a document repository and database
web front-end for the client's existing Oracle database of fisheries
data. J2EE, J2SE web application running on Tomcat 5 using Struts,
Velocity, Hibernate, and DBCP= .

_Lease Data Manager, ConocoPhillips Alaska_
Technical lead for a an oil-field lease data management application
consisting of business-logic, DB interaction and a Windows desktop
front-end written in VB.NET and running against an Oracle and Oracle
Spatial/SDE back end

_Pebble Mine Website, Northern Dynasty_
Architect and lead developer for a mission-critical project intranet
for gathering and maintaining environmental data and coordinating
activities of project team members for the largest gold mine in North
America. ASP.NET web application with Oracle back-end, written in
C#.NET, and Oracle PL/SQL.


##### Senior Web Application Developer, Technical Lead, XP Coach -- Unext.com, Deerfield, Illinois

UNext.com (now known as Cardean Learning Group) is an e-learning
company providing web-based graduate and undergraduate degrees and
executive education programs

_Tech: Red Hat/Fedora and Windows 2000; Java, JavaScript, VB,
VBScript; Jetspeed, Velocity, Tomcat, JSP, ASP; MySQL, Oracle,
PostgreSQL; JBuilder, NetBeans, Eclipse, Visual Studio; XSLT, XML,
Xerces, Xalan; ActiveX, ADSI, COM; XMetaL, Documentum, Broadvision;
Ant, CVS, LDAP, and CruiseControl_

_XP Coach, Software Architect_
Architected and implemented portions of ECAS, a student information
system, implemented as a J2EE web application on Tomcat with Jetspeed
portlets rendered with JSP and Velocity with Hibernate ORM and data
stores in Oracle and PostgreSQL.

_Senior Web Application Developer, Technical Lead_
Contributed to Edge, a new content-authoring system and asset-catalog
integration including an asynchronous discussions subsystem and
content loading and exporting using JSP and Servlets, with data stores
Oracle 8i. Implemented build and CI systems for both using
CruiseControl, Ant, JUnit, Clover, and jcoverage.

_Web Application Developer_
Developed DTD and XSLT components; designed and implemented
event-driven UI; and contributed on COM integration for Unext's first
content repository in Documentum. Worked on a small rapid application
development team prototyping fully functional ASP database-driven
courses with overnight turnaround.

#### Education

##### Florida State University, Tallahassee, Florida
Bachelor of Science in Economics, minor in Mathematics

##### Centre College, Danville, Kentucky
Economics and physics major, computer science and mathematics minor
