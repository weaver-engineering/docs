# Writing Specs

## Context
- [defining-services](defining-services.md) - notes on defing services

## What A Spec Is
A spec is a specification to change a service. It is not a specification of the service itself. The service should be defined separately to the spec. The spec defines the behaviors of the service to be implemented. It may also define prior behaviors that should be removed or modified.

Behaviors are defined in a Given/When/Then format. In practice the conditions that form the basis of the services behaviors are often best considered as a hierarchy. E.g.
* Given the user John 
	* With a valid auth token abcd
	* Without an auth token
	* With an invalid auth token zzzz
* Given a blocked issue
	* With high priority
		* In alarm state
		* Not in alarm state
	* With medium priority
		* In alarm state
		* Not in alarm state
	* With low priority
		* In alarm state 
		* Not in alarm state 
As such they can be referenced using an N.M.O... numbering system. The behaviours themselves are not owned by the spec, they are owned by the service whose behaviors they define. The behaviours must also define the interaction with the service and the expected result in terms of outputs and interactions with dependencies. 

A spec is the instruction to implement a set (1 or more, possibly all) of behaviors for a service.

A spec therefore cannot exist without a definition of the service whose behaviors are being implemented. 

