# Webserver

- stores web pages, contents over the internet
- operates on HTTP, hyper text protocol

## Webserver Architecture

- single-tier
    - responsible for processing requests and serving web content
    - suitable for small websites or applications with small traffic
    - limitations on scalability and fault tolerance

- multi-tier (Load balanced)
    - using multiple servers to distribute the workload
    - load balancing
    - ensure high availbility and scalability
    - evenly distribute incoming requests across a cluster of web servers
    - redundancy