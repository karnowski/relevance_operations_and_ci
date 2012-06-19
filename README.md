# Automated CI & Operations at Relevance

## Agenda (45 minutes total)

- Intro (5 minutes)
- Talk (25 minutes)
	- ...
	- ...
- Q&A: (15 minutes)

## Outline

### Relevance is a Consultancy
- we don't normally support a product in production
- we're optimized for greenfield product creation
- then we hand over the keys to our clients operation team
- we have no ops -- no sysadmins or DBAs, not even a QA staff

### ClientOps
- we do all testing and operations by developers
- Usually our clients are coming to us to be trained.
- Often, our biggest clients have an internal ISP/cloud that we cannot touch directly.  We have to give their ops teams direction without being able to see the production environment ourselves.
- Client picks the deployment environment
	- database, CentOS/Ubuntu/FreeBSD

### We're automation addicted.	
- To deal with all this, we've become automation-addicted.  We have repeatable provision scripts for almost all of our projects.
- More and more of these provision scripts are being ran nightly by our CI server.
- old-school: plain old Bash, but idempotent
- Ruby stack: Chef + Fog + Net:SSH (slushy)
- JVM stack (JRuby & Clojure): Pallet

### How do we practice our operations?  On ourselves!
- Our CI server is vulnerable
- It represents dozens of man-hours.
- It changes regularly as we add/remove projects and their dependencies.

### Continuous integration
- we use Jenkins (very configurable, has lots of plugins, Chuck Norris)
- we have anywhere from 12 to 20 active projects at any given time (15 as of the time of this writing)
- we have several build environments:
	- ruby (1.8.7, 1.9.2, and 1.9.2)
	- jruby (java)
	- Clojure (java)
- we have several databases
	- postgresql
	- mysql
	- mongo
	- datomic
	- MSSQL Server (sometimes)
- we sometimes have to support builds on Windows

### So we rewired it!
- Using Chef, Fog, Net:SSH
- Our tools: Elzar, Flexo, and Slushy
- Deploying master to EC2
- Jenkins master can "summon" build slaves ("Elastic CI")

### Flexo
- private (for now) due to security concerns we're addressing
- we'd like to open source if there's interest and time allows
- leverages slushy
	- uses Fog to provision an EC2 instance
- jenkins-config 
	- a "meta-build" in jenkins that backs up our config to GitHub regularly
	- backs up all config, but no build state

### Which cloud?
- Considered Linode
- reserved instance of EC2 will be cheaper for master ($$$?)
- will be more expensive with combo of master + slave, but we're getting more capacity

### What did we not use?
- Pallet -> next likesly candidate; Clojure based; solves the "must have ruby on box to bootstrap" issue as it only requires SSH & bash
	- If you're using Clojure or Java, you might want to look at Pallet.  That one tool, albeit with a difficult API, encapsulates a lot of the tools we've cobbled together (Chef, Fog, Net::SSH, and Slushy)
- Puppet --> we're rubyists, and we're opinionated on 

### Some best practices
- highly recommend building any provisioning code every night
	- the biggest risk to provisioning process isn't your code but but the external Internet
	- you want to lower the risk that something has moved out from under you since you last provisioned, so ALWAYS be provisioning
	- EC2 makes this easy and cheap
- don't store your configuration as files in your code base 
	- 12factors.net
	- use environment variables instead, we're hurting from this right now

### Problems We're Tackling Now
- cleaning up some security issues
	- this will also let us opensource Flexo
- optimized creation of build slaves; taking a long time right now
- moving away from RVM for the love of God
	- RVM is extremely difficult to automate around; changes so much we're having difficulty keeping things stable


-------------

ROUGH NOTES

-------------

# Automated CI & Operations at Relevance

## Agenda (45 minutes total)

- Intro (5 minutes)
- Talk (25 minutes)
	- ...
	- ...
- Q&A: (15 minutes)


## Brainstorming

- We don't have an official Operations team at Relevance, so developers doing Ops is our standard.
	- no sys admins
	- no DBAs
	- we build mostly greenfield systems for our clients, then hand over the keys to there operations staff

- We're different, we're a consultancy
	- we have to support multiple tech stacks
	- secret: you can't be an expert in all tech stacks!
	- we've developed ways to cope with that, namely continuous integration
	- our clients often come to us to learn the "new hotness" technologies
		- automation sometimes serves as training and documentation to our clients

- Client picks the deployment environment
	- some of our biggest clients don't let us touch the production systems AT ALL
	- we have to go through intermediaries for everything
	- how can we deliver value when we can't control how or what gets installed?
		- by empowering the client's ops teams with automation!  (for great good!)

- How do we practice our operations?  On ourselves!
	- automating our key servers: CI, card tracking systems, and continuous deployment to our www.thinkrelevance.com website

- Continuous integration
	- we have anywhere from 12 to 20 active projects at any given time (15 as of the time of this writing)
	- we have several build environments:
		- ruby (1.8.7, 1.9.2, and 1.9.2)
		- jruby (java)
		- Clojure (java)
	- we have several databases
		- postgresql
		- mysql
		- mongo
		- datomic
		- MSSQL Server (sometimes)
	- we sometimes have to support builds on Windows

- Problems with CI
	- cobbled-together by many hands over a long period of time
	- new projects often bring in new dependencies that might interfere with old projects
	- old projects are not being built regularly, so we don't see "CI drift" issues until too late
	- not enough CI capacity -- some builds are really freakin' big
	- complicated, shared resources requiring latches & locks to build serially
	- taken together, we were sometimes seeing our builds lag after a commit by several hours --> defeats a lot of the purpose of CI by missing quick feedback

- CI Build slaves
	- master server has build slaves, either dedicated (like a Windows machines) or shared
	- build slaves can be elastic (NEED TO EXPAND)
	- working on build slaves that allow us different capabilities
		- for example, rbenv vs rvm

- Open Source Tools
	- Chef
	- Fog
	- Net::SSH
	- Jenkins
		- Jenkin's plugins (elastic, what else?  chuck norris?)

- Our open source tools
	- relevance_rails (generates Rails apps in a Relevance-opinionated way and includes easy Amazo EC2 provision & deploy scripts)
	- Elzar (our home-grown Chef recipes, tested nightly)
	- slushy (use Fog to provision, then get Chef-solo into the kitchen)




- Problems
	- "CI drift" - we must build our idle projects nightly otherwise we don't find problems until it's too late




