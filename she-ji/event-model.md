


# 对象


1. visitor

#Event

## New visitor
### will
  - check domain restriction
### do
  - create visitor
### did
  - calc segments

## Page visit
  - actions
    - page visit
    - get chat button 
    - check if online
    - restore chat
    - check manual invitation
    - check auto invitation
  

### will
### do 
  - update visitor info - current page
### did
  - calc segments
  - calc conversion - match, send message to mq
  - calc dynamic campgin
  - check domain restriction
  - calc route
### return
  - chat button
  - restore chat
  - manual invitation
  - auto invitation


## heartbeat
    - check manual invitation
    - get if online
### will
### do
### did

## Custom Variables
### will
### do
    - update visitor's custom variables
### did
    - calc segments
    - calc conversion - match, send message to mq
    - calc dynamic campaign
    - calc route
    - trigger bot event

2. chat
