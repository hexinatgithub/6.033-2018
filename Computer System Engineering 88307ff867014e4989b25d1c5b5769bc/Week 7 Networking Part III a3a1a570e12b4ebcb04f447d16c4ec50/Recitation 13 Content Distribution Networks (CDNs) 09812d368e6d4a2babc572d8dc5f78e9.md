# Recitation 13: Content Distribution Networks (CDNs)

> What are Akamai’s design goals?
> 

A delivery network is a virtual network built as a software layer over the actual Internet, deployed on widely distributed hardware, and tailored to meet the specific systems requirements of distributed applications and services. A delivery network provides enhanced reliability, performance, scalability and security that is not achievable by directly utilizing the underlying Internet.

> What happens when a user visits a particular URL? How does the content get to their machine?
> 

When the user types a URL into his/her browser, the domain name of the URL is translated by the mapping system into the IP address of an edge server to serve the content. To assign the user to a server, the mapping system bases its answers on large amounts of historical and current data that have been collected and processed regarding global network and server conditions. This data is used to choose an edge server that is located close to the end user.

Each edge server is part of the edge server platform, a large global deployment of servers located in thousands of sites around the world. These servers are responsible for processing requests from nearby users and serving the requested content.

In order to respond to a request from a user, the edge server may need to request content from an origin server. For instance, dynamic content on a web page that is customized for each user cannot be entirely cached by the edge platform and must be fetched from the origin. The transport system is used to download the required data in a reliable and efficient manner. More generally, the transport system is responsible for moving data and content over the long-haul Internet with high reliability and performance. In many cases, the transport system may also cache static content.

> The paper differentiates between a “content delivery network” and an “application delivery network.” What is the difference between “content” and “applications,” and why does this difference matter to Akamai?
> 

A **content delivery network (CDN)** is a geographically distributed group of servers that caches content close to end users. A CDN allows for the quick transfer of assets needed for loading Internet content, including HTML pages, JavaScript files, stylesheets, images, and videos. CDNs are designed to optimize the delivery of **static content**.

On the other hand, an **application delivery network (ADN)** is a suite of technologies that, when deployed together, provide availability, security, visibility, and acceleration for Internet applications such as websites. ADN components provide supporting functionality that enables website content to be delivered to visitors and other users of that website, in a fast, secure, and reliable way. ADNs optimize the acceleration of **dynamic content**.

The difference between the two lies in the type of content they optimize. CDNs focus on **static content** such as images, videos, and other assets that don’t change frequently. ADNs, on the other hand, optimize the acceleration of **dynamic content** such as web applications that require real-time data processing and user interaction.

This difference matters to Akamai because it is a company that provides both CDN and ADN services. By offering both types of services, Akamai can provide a comprehensive solution for optimizing the delivery of both static and dynamic content.

> Why wouldn’t a peer-to-peer network suffice for Akamai’s purposes?
> 

Lack of management and control features in current implementations make them unsuitable as stand-alone solutions for enterprise quality delivery.

> What aspect(s) of the Internet’s infrastructure is Akamai’s platform designed to overcome?
> 

Peering point congestion, Inefficient routing protocols, Unreliable networks, Inefficient communications protocols, Scalability, Application limitations and slow rate of change adoption.

> How is the platform designed to overcome those aspects?
> 

Delivery Networks as Virtual Networks Conceptually, a delivery network is a virtual network built as a software layer over the actual Internet, deployed on widely distributed hardware, and tailored to meet the specific systems requirements of distributed applications and services. A delivery network provides enhanced reliability, performance, scalability and security that is not achievable by directly utilizing the underlying Internet.

> Why is it necessary for Akamai to overcome those aspects?
> 

Bridging the technological gap between the limited capabilities of the Internet’s infrastructure and the performance requirements of current and future distributed applications is thus critical to the continued growth and success of the Internet and its viability for business.