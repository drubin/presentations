class: center, middle

# Container Experiences 

#### Hard Lessons learnt

![Logo](olx.jpg) 

.left[
David Rubin]
.left[
[<i class="fa fa-twitter" aria-hidden="true"> drubin87</i>](http://twitter.com/drubin87)]
.left[
[<i class="fa fa-github" aria-hidden="true"> drubin</i>](http://github.com/drubin)]
.left[
[<i class="fa fa-linkedin" aria-hidden="true"> David Rubin</i>](https://www.linkedin.com/in/davidrub)]

---

# Me 
![Me](tablemountain.jpg) 
???
- From Cape Town 
- Office by the ocean
- 
---

# Stats 
![Stats](stats.png)

---

# Beginning 
![Stats](beginning.png)

---
class: center, middle

# Why 

##Increasing confidence, predictability and consistency

---
# The “able”s

* Composable
* Predictable 
* Reproducible
* Versionable 
* Auditable
---
class: center, middle

# Consistency
### Consistency is often better than correctness * 

*https://en.wikipedia.org/wiki/Worse_is_better*

---
class: center, middle
# Optimise for change 
---
# Compromises 

![Peg](peg.jpg)
---
class: center, middle
# Monitor Everything 

### Newrelic
### Sysdig Cloud

Exploring prometheus

---
## Load Testing

![Goad](goad.gif)
---
class: center, middle
## Sometimes your monitoring lies 

....

#### Memcache 
vs
#### DNS

*60k DNS queries per second per node*

---
# Docker Layers 

Simple 

```docker   
FROM python
ADD src .
RUN pip install -r requirements.txt --no-cache-dir
```  

Better

```docker
FROM python
ADD src/requirements.txt .
RUN pip install -r requirements.txt --no-cache-dir
ADD src .
```

Order matters, leveraged Caching 

---
## Local Development 

* Minikube 
* Like Vagrant for kubernetes 
* Single Node cluster 
* Missing platform features such as Loadbalancers
* Replacement for Docker Compose 

---
# Continous Delivery 

* Jenkins build pipelines 
* Multi node builds 
* All in EC2 
* Automated deploys
* Manual approval proccess to production

---
# Gotchas

* No SSH 
* Imutable 
* No writeable filessytem (well its wiped every deploy)
* No *tail -f* centeral log system
* Build tools, not run books/docs 

---
# Links

** Presenation ** 
* Slides https://drubin.github.io/presentations/ 
* Goad https://github.com/goadapp/goad 
* MiniKube https://github.com/kubernetes/minikube 
* Jenkins Pipelines https://drubin.github.io/presentations/2017/devops-meetup-jenkins/#1 

 --- 
.center[**Thanks**]
