# PHP Versions compatibility 



## EvolutionTracking Strategy

### Preferred PHP Version

The preferred PHP version is always the latest official version supported by the PHP community.

### Continuous Integration/Continuous Deployment (CI/CD) Pipeline

A CI/CD pipeline is maintained to automate testing processes.
The official branch uses a CircleCI workflow and config file source code can be found at the following URL: [CircleCI Configuration File](https://github.com/equalframework/equal/blob/master/.circleci/config.yml).

### Testing and Support

Testing and support are consistently provided for the current PHP version and the two previous PHP versions (n-2). 

This ensures backward compatibility and facilitates the identification of potential breaking changes as they arise.



## Supported PHP Versions historical Chart

|eQual Version / PHP Version|7.4|8.0|8.1|8.3|
|-:|--|--|--|--|
|1.0|Y||||
|2.0|Y|Y|Y|Y|