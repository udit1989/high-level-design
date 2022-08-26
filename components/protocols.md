# Protocols


### overlapping terminology used in this doc
- resource, DOM, node -> a unit of data container, which contains multiple hetrogenous data objects.
# Protocols options between client-server

This is communication one needs to select between your client and server. 

## comparision table 

- Low Latency 
- Low Bandwidth
- Decouples client & server (flexibility to change things)

Options 
1. REST 
2. RPC
3. GraphQL


To a great extent what I understand is, one should choose the method based on the database they have and the type of query they'll be making from client.

### Rest 

Rest explaination :

[REST ELI5](https://www.reddit.com/r/explainlikeimfive/comments/a1axe2/eli5_what_is_restful_api_and_how_does_it_work/)

The most important part of rest is that it works in resources, the DOM based calling when your objects in database is not overlapping.

Or it can be taken as a default option to be picked.

- Low Latency : NO, almost n number of calls needed for n non-overlapping resouces.
- Low Bandwidth: NO : Rest return complete resource which might be having extra information.
- Decouples client & server: YES, since it already calls the complete resource.


### RPC - Remote procedures calls

RPC explaination:

[RPC Dev.io](https://dev.to/aafrey/eli5-remote-procedure-calls-pkg)

In simplest term, it feels like client is calling a function, and you can make function to whatever component you need, which can be a complete DOM resource, of specific objects inside it.

Now, this method is preffered when you've your own build client, for example an android app, which talks to the server, since in that case, the android app is build internally, and is easily aware of data strucutres. 
On the other hand if this was based on a public API it cannot be very efficient, since the structure of your data cannot be exposed to the public.

- Low Latency : YES, one can ask for all resources in one call.
- Low Bandwidth : YES, one can get exactly what data is needed, which can be selected from the DOM.
- Decouples client & server: NO, since one needs to amend a function or make a new function, both on client and server.


### GraphQL

GraphQL explaination:
[GraphQL freecodecamp](https://www.freecodecamp.org/news/so-whats-this-graphql-thing-i-keep-hearing-about-baf4d36c20cf)

graphQl is usually preffered when there is a requirement for application to work in low-bandwidth.
Like WhatsApp, as one might have observed WhatsApp's text messages still work in worst of the networks and lowest bandwidths.

In graphQL one cannot fetch a complete resource, but is only allowed to get specific objects. By this is makes the client only ask for the specific data need and not extra or redundant data which needs extra bandwidth.

Better is used with graph based data model.

- Low Latency : YES, one can just ask for all resources in one go, which can be from different resources.
- Low Bandwidth : YES
- Decouples client & server: YES, since one just need to change change the query asked like the REST protocol.


# Protocol intra-server 

The interactions within the components of a server is usually limited and are bound to change less over the period of time, and if there is a change it can be easily updated on both the ends.
Also RPC works with low bandwidth which recudes the number of intra-components calls, which reduces the overall cost of operations happening within the server.

Consider this case one can easily pick <b>RPC</b> for intra-server calls.
