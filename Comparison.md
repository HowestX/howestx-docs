# A comparison of existing open source MOOC platforms.

If a modern school is interested in offering the capability of massive online learning, a MOOC platform is the way to go. However, developing one is an extremely complex and time consuming process. Luckily there are already a lot of free open source MOOC platforms that could, in theory, be easily adapted to fit a school’s specific needs. Here we are going to briefly compare a couple of MOOC platforms.

We will compare the following platforms:
* edX
* Peer 2 Peer University
* openMooc

We are interested in the following criteria:
* Usability, is the platform easy to use as an end user?
* Activity, is the platform still actively developed?
* Complexity, is it easy to adapt to our needs?

## edX
EdX is a large open souce MOOC platform that was developed as a joint venture between the Massachussets Institute of Technology and the Harvard university. UC Berkeley has also joined.
The platform already has a lot of succes, boasting more than 3 million users (as of October 2014), and being used by institutions such as stanford.

### Usability
EdX as a platform is very usable to an end user, it offers an intuitive user interface which is alo rather attractive to the eye. This does not translate over to mobile, as the default theme is not mobile ready.

### Activity
We were happy to see edX being a very active platform. Not only does it have a remarkeable amount of users, it also has a lot of developers behind it. When looking up the github page of the platform, we saw commits as recent as 2 hours ago.
It’s also a rather ‘hot topic’ online, showing a surge in activity.

![Google trends graphic on edX](https://github.com/thomastoye/howestx-docs/blob/master/images/GoogleTrends_edX.png "EdX on Google trends")

### Complexity
EdX so far appears to be the perfect choice for any school interested in setting up a MOOC platform of their own. However, it’s sheer complexity might cause some issues.
EdX is an absolutely huge platform, that is also highly fragmented. Simply finding the source code of a specific part of it is a huge task on it’s own.
However, there is also an immense amout of documentation and an active community ready to help. Even edX claims to offer support to an institution attempting to adapt edX to it’s own needs.

## Peer 2 Peer University
P2PU was founded with funding from the Hewlett foundation, the Shuttleworth foundation and the university of California Ivine. It was founded by a group of people that felt the existing ways of online learning were inadequate, especially the social aspect.

### Usability
P2PU also offers an attractive user interface, yet we felt it was not as intuitive as it should be.

### Activity
It’s definetely still used by a large user base, but it shows signs of decline. It’s github is not that active and has open issues as old as 3 years (the oldest bug dates from 25 May 2012). Google trends also reveals a steady decline.

![Google trends graphic on P2PU](https://github.com/thomastoye/howestx-docs/blob/master/images/GoogleTrends_P2PU.png "P2PU on Google trends")

### Complexity
P2PU is what could be called a medium sized platform. It appears to be a nicely organised platform, that should be easy to adapt. It also offers quite a bit of documentation. The big issue for us is the fact that there are so many old open issues. Is the development grinding to a halt?

## OpenMooc
OpenMooc is a MOOC platform that hails from Spain, it was made to promote virtual education by using ICT in higher education. Since it was made in Spain, multilanguage support has always been available.

###Usability
OpenMooc’s user interface is really atrocious. A lot of work would have to be put in to make this a usable platform. Nevertheless it has some interesting usability features built-in, most notably multilanguage support.

### Activity
As far as we can tell, OpenMooc isn’t a widely used platform. A couple of smaller institutions allegedly use it but there is no proof of any major organisation showing interest in this platform. It’s github is also very inactive, most commits are already a year old. Google trends also confirms this.

![Google trends graphic on OpenMooc](https://github.com/thomastoye/howestx-docs/blob/master/images/GoogleTrends_OpenMooc.png "OpenMooc on Google trends")

### Complexity
This is a rather small MOOC platform. It seems rather well organised, but very badly documented. Also setting up this platform seems to not be a trivial matter.

## Conclusion
There are a lot of platforms out there, these platforms seemed to be the most prominent. 
They all offer the ability to host a decent MOOC platform yet edX and P2PU are by far the largest and offer the most usability out of the box. Deiciding between these two is not easy, edX offers more functionality whereas P2PU offers a seemingly easier to adapt codebase. However, edX is seeing way more use and development. P2PU as a platform seems to be heavily on the decline so trying to build a lasting MOOC solution based on that platform appears unwise.
In the end, edX seems the best choice. It may be a complex system that will require some thorogh analysis but it’s active development and good support and documentation should more than make up for that.
