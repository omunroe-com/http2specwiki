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

Therefore, Darlene has a strong incentive to save bandwidth. By intercepting and transcoding Web traffic -- e.g., images and especially videos -- she can increase the capacity of her network, often (but not always) without the end user noticing (since they tend to be using smaller screens than the original video was intended for).

While Darlene used to apply these optimisations across the board, recently she has been using products that adapt to load and "hot spots" in her network, so that she only transcodes when she needs to.

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

