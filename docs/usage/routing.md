### Router 

Structure 	URI -> operation

Reserved parameters
	get, do, show, route, anounce

routing to scripts

	simple scripts 
	
	Main contraint : routing and FS tree

Turns a script into a micro-service

1) description
	* what it does
	* expected parameters and related characteristics
		* constraints, default value, optional or mandatory; type; name; description
2) dependency injection
	services required by the script

3) response format : content-type and content-disposition (charset)

4) CORS : accept-origin (Access-Control-Allow-Origin)