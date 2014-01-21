_This is a list of user stories for HTTP proxies. Feel free to elaborate or add new stories._

## Alex's Enterprise-Supplied Laptop

Alex's employer gives him a laptop computer, and when it's connected to their network, it should automatically use their proxy. Because the laptop is owned by his employer, they've installed a CA in its trust store, so that they can intercept traffic from it (as per his terms of employment). 

It would be nice if Alex were reminded that this was happening while he was browsing the Web, but really he should know this anyway.

Also, Alex's browser would like some indication of the quality of encryption on the "other side" of the proxy.

## Bobbie-Sue's BYOD at Work

Bobbie-Sue's employer allows her to use her own devices (laptop and phone) at work. However, to access some company assets, she needs to install a CA in her device trust store(s). 

Bobbie-Sue trusts her employer, but doesn't want to let them intercept traffic to other sites; she should be able to either limit the ability of the CA to her company's sites, not allow it to be used for interception at all, or detect when it's in use for interception.

## Charlie's Coffee Shop

Charlie runs a coffee shop that provides wifi to its customers. Before they use the network, however, he needs to show them a "terms and conditions" page, and perhaps get login details from them.

Charlie doesn't want to intercept his customers' traffic after that, but he does need a way to show them the network login page, even when the browser tries to go to a HTTPS site.

## Darlene's Mobile Network

Darlene is an executive at a mobile provider, and is responsible for building out their network. If Darlene's customers use too much bandwidth, she needs to deploy very expensive cell towers to provide capacity, which in turn means she needs to raise prices, potentially making her company less competitive.

Therefore, Darlene has a strong incentive to save bandwidth or downgrade QoS for a category of traffic. By intercepting Web traffic, she can increase the capacity and responsiveness of her network by transcoding or downgrading QoS for content -- e.g. images and especially videos -- often (but not always) without the end user noticing (since they tend to be using smaller screens than the original video was intended for).

While Darlene used to apply these optimisations across the board, recently she has been using products that adapt to load and "hot spots" in her network, so that she only optimises when she needs to.

## Eliot's Village Internet Connection

Eliot lives in a remote village where Internet connectivity is quite poor, both in terms of bandwidth and latency. His co-villagers spend a lot of time browsing the Web, and so would benefit from a shared caching proxy.

More encryption of HTTP makes it difficult for the village to deploy a proxy; while CDNs help "big" sites, it doesn't help the rest of their Web traffic, and doesn't even help the "big" sites unless the CDN has a node deployed on their side of the bottleneck.

## Francine's Virus Scanner

Francene's computer has a virus scanner that needs to inspect all traffic -- encrypted or not -- as it goes by, and potentially modify responses ("cleaning" them), or rejecting them outright.

The virus scanner achieves this currently by inserting a CA into Francene's trust store and performing TLS MITM on it.

## Grant's Debugging Proxy

Grant uses a proxy to debug his application, and needs "raw" access to the data stream. Like Francene, he can achieve this by inserting a CA into his trust store and MITM'ing the stream, but this is cumbersome.

## Henrietta's Jailhouse Schoolroom

Henrietta runs an educational institution inside a house of incarceration (since they share so many attributes). While her student inmates are allowed to surf the Web, they are not allowed to access prohibited resources -- indicated by URL as well as content. 

As a result, Henrietta needs to see **all** traffic that goes by.

## Ian's Compliance Mission

Ian is an executive in a fictional ethical banking institution. He needs to ensure that his employees are complying with various legal requirements as they use the Web. In particular, he wants to assure that proprietary and sensitive information is not leaving via upload forms, etc. 

As a result, he needs access to all outgoing content.

## Jane's Thing

Jane has a new Internet of Things device of some sort in her house, but she also has an access gateway that proxies all HTTP to the outside world. As such, she needs to easily configure it to use the proxy.

## Dan's Kids

Dan's kids use all kinds of internet-connected devices in his family home, such as laptops, desktops and phones. He wants to maintain a proxy that limits access to certain Web sites from any of these devices attached to his home network to keep his kids on the safer side of the Web.

## Gautam's Mobile Identity Proxy

Gautam runs a Mobile Identity Proxy in a Mobile Network and needs to add the real/anonymous mobile identity of the user as HTTP header, to facilitate the seamless authentication of the user by the service provider. 

## Bob's Music Service

Bob manages a very popular Rock music web site based on individual subscription and per month fee. To develop his business he partnered with a mobile operator who wants to provide rock music as bonus to his premium data subscribers.
Alice is fond of Rock music. Thanks to her premium data subscription plan, Alice has an unlimited access to a Rock music web site. Thanks to service augmentation Alice has a seamless access to its favorite streaming music. Alice loves this seamless approach which protects her privacy and avoids her to remember yet another login and password couple. 

## Diane's kids' mobile devices

Diane's kids use mobile devices such as phones and tablets outside home. Diane wants to sign up those devices with mobile operator's parental control service which is set up through a proxy to limit access from those devices to certain web sites to keep her kids browsing safely also limit certain contents like video streaming when close to the data plan quota in order to keep the data usage under control and no surprise bill.

## Bob’s Certification Company
 
Bob runs an on-site education testing and professional certification company. His clients are students that need to take the online tests like TOEFEL, SAT as well as individuals required by their profession to getting/renewing their certification.
Due to the nature of the business and in order to prevent cheating, Bob has restricted access to only the online sites offering the tests during the testing hours. In order to maintain his credibility with the certification organizations he needs to make sure that employee authenticate and their access to the testing sites is monitored for audit purposes.
 
## Alice’s Flowers delivery company

Alice owns a flower delivery company and just she started taking orders online. She got feedback that while some of his customers are frequently ordering from their mobile phones, others call in because they don’t want to pay for the data. In order to grow the business and reduce costs associated with the dedicated phone order representatives, she partnered with several mobile operators to cover and pay for the data usage associated with his customers’ access to her website.
 
## Rick’s Online Movie download store
 
Rick runs successful business allowing his customers to download movies for rent or to buy.  His customers are increasingly using their smartphones to view the movies on the go but a common complaint was that with the increased size of the downloads  they had no way to intuitively determine if they reached their data plan limits and incurred significant overcharges.
Rick had partnered with several mobile operators and his developers are able to receive in real-time (in a form of a custom header) the remaining data value for the current billing cycle. With this enhancement, Rick was able to preemptively and accurately notify his customers when they were close to reaching their limits without the need for his users to disclose their phone numbers and therefore minimize the information he collected from them. 
