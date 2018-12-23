# wiki-crawler

distributed wikipedia crawler

## requirement

- design a program to crawl wikipedia and clone the content to dedicated storage

### functional

- the program can access wikipedia content via its public https interface
- it should be able to orchestrate multiple servers with arbitrary capacity
- whenever wikipedia content are updated, the program is able to sync up and update its content

### non-functional

- minimum overall latency to sync up content upadte from wikipedia to dedicated storage
- servers are not stable which can be up and down, which should not affect the functionality of the program

## design

### data model

- page: a wikipedia page which URL, content and the links to other wikipedia pages
  - attributes
    - URL
    - content
    - links
    - lastUpdated
  - operations
    - create
    - update
    - delete

- priority queue: a queue of page URLs sorted by lastUpdated timestamp

### controller

The controller constructs the priority queue of pages, and seed the first wikipedia pages to the queue. It publishes below APIs for the workers:

- task/get: get the first task (page to parse) from the priority queue
- task/post: add new tasks (pages to parse) to the priority queue
- page/get: get a page by URL
- page/post: create or update a page

### worker

The worker machines keep pulling tasks from the priority queue via task/get, and process below steps in a job:

- curl URL
- construct a page
- post the page to page/post
- post the links of the page to task priority queue