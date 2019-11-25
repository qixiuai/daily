# daily

## 11.25 - 12.1

### 11.29 Friday
- [ ] 
- [ ] multi-scale tridenet

### 11.28 Tursday
- [ ] eigen
- [ ] centernet 0.40

### 11.27 Wedsday
- [ ] circular ost
- [ ] uda >= 0.35

### 11.26 Tuesday
- [ ] order statistical tree
- [ ] randaug >= 0.30

### 11.25 Mon
- [ ] red black tree
- [ ] detectron2 baseline

### 11.14 - 11.20
- [ ] 

### 11.11
- [ ] PSG C++
  - [ ] supports
	- [ ] del edf file
    - [ ] filters
    - [ ] parameter ip and port
	- [ ] add schedule
  - [ ] analysis performance
	- [ ] resp event parallel
	- [ ] stage unit refactor : global object
	- [ ] del part session not all

- Base
  - Inferface
  - Context, NameScope
  - Module[stateful, cache results]
	- analysis units: Light, Stage, Resp ...
	- report units: Stage, Resp, Leg ...
  - Modules: Parallel or Serial
  - SerialModules: level 0, 1, 3, 3 4 ...; [0, 1(1, 2), 2, 3]
  - Server

- level
  - [0, 1, 2(2,2), 3, 4]
  - [0, 1, 2(2,3), 3, 4]
  - [0, 1, 3(2,3), 3, 4]
  - [0, 1, 3(2,3), 4, 4]
  - [0, 1, 3(2,3), 4, 5]
  - [0, 1, 4(4,3), 4, 5]
  - [0, 1, 4(4,3), 5, 5]
  - [0, 1, 4(4,3), 5, 6]


- [ ] UDA Framework
  - [ ] dataset
  - [ ] model
  - [ ] gin conf interface

### 9.28 Saturday
- [ ] find why models bad and how to make it better
- [ ] a state of art model

### 9.27 Friday
- [ ] train on sleep-edfx

### 9.26 Tursday
- [ ] train on isruc

### 9.25 9.24 9.23 Monday Tusday, Wedsday
- [ ] new architecture based on SoftPAI
- [ ] better architecture of pai
  - [ ] various dataset and datasets concatenatation
  - [ ] various models
	- [ ] resnet
	- [ ] resxnet
	- [ ] SENet
	- [ ] DenseNet
	- [ ] attention
	- [ ] transformer
  - [ ] various loss
  - [ ] various metrics
  - [ ] vis
	- [ ] activation analysis
  - [ ] serving
  - [ ] flags
  - [ ] tpu support
  - [ ] gcloud support
- [ ] custom tensorflow c++ ops

### 9.23 Monday
- [ ] fix restore bug
- [ ] fix memory bug

### 9.22
- [ ] CenterNet object as points

### 9.21
- [ ] better architecture of pai
  - [ ] various dataset and datasets concatenatation
  - [ ] various models
	- [ ] resnet
	- [ ] resxnet
	- [ ] SENet
	- [ ] DenseNet
	- [ ] FracalNet
	- [ ] attention
	- [ ] transformer
  - [ ] various loss
  - [ ] various metrics
  - [ ] vis
  - [ ] serving
  - [ ] flags
  - [ ] tpu support
  - [ ] gcloud support
  - [ ] activation analysis
- [ ] custom tensorflow c++ ops

### 9.20
- [x] focal loss
- [x] hourglass
	
### 9.19
- [ ] improve f1-score to 83%
  - [x] focal loss
  - [x] stanfordNet
  - [x] revise pai

