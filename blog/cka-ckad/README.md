# Tips to ace in CKA and CKAD exam

| ![cka-ckad](https://github.com/milindchawre/civo-k8s/raw/master/blog/cka-ckad/images/cka-ckad.png) | 
|:--:| 
| *CKAD and CKA Certificate* |

I recently passed both CKA and CKAD exam, scoring 98% and 96% respectively. In this blog, I want to share my overall experience and tips to crack these exams.

#### What is CKA and CKAD?
CKA is [Certified Kubernetes Administrator](https://www.cncf.io/certification/cka/) exam which focuses on administrative skills of managing, troubleshooting and operating a kubernetes cluster. The CKA exam certifies that users can design, build, configure, and expose native cloud applications for Kubernetes. 

CKAD is [Certified Kubernetes Application Developer](https://www.cncf.io/certification/ckad/) exam which focuses on developer skills of kubernetes cluster. The CKAD guarantee that certification holders have the knowledge, skills, and capability to design, configure, and expose cloud-native applications for Kubernetes and also perform the responsibilities of Kubernetes application developers.

These certification are valid for a duration of 3 years (36 months).

#### Which exam should I opt for?
You can attempt both the exams, but CKA is more suitable for administrators while CKAD is more inclined towards developers. Decide and attempt.

#### Exam Curriculum (as of v1.19):

Both the exams are delivered online and consist of 15-20 performance-based tasks with 2hrs duration.

---
>**_CKA (~ 17 questions)_**

25% - Cluster Architecture, Installation & Configuration

15% - Workloads & Scheduling

20% - Services & Networking

10% - Storage

30% - Troubleshooting

---
>**_CKAD (~ 19 questions)_**

13% – Core Concepts

18% – Configuration

10% – Multi-Container Pods

18% – Observability

20% – Pod Design

13% – Services & Networking

8% – State Persistence

---

To know more, check this [official site](https://github.com/cncf/curriculum) for the exam curriculum.

#### Exam Pre-requisite:
- Register for the exam. Once you register, it is valid for 1 year. So you have one whole year to schedule the exam. Also you have one free retake in case you fail the exam in first attempt.
- vi basics
- Linux basics
- Chromium based Browser. Make sure your browser pass [compatibility check](https://www.examslocal.com/ScheduleExam/Home/CompatibilityCheck).

##### Exam Preparation:

There are [lot of resources](https://www.google.com/search?q=cka+ckad+exam+tips) available out there to prepare for the exam. I am listing those which I followed:
- Enroll in kodekloud udemy course for cka and ckad. Course link mentioned below.
- These courses covers all the concepts and have lots of practice labs to prepare you for the exam, also they are very cheap less than $5.
- After enrolling in the course, you also gets access to the kodekloud slack community; where you can ask your doubts and learn from others.
- Once you complete these courses, practice all the labs and mock tests multiple times. Atleast 3-4 times, until you get to the speed (completing the tests within half time) and scoring 100%.
- Practice more questions, mentioned in the link below.

Following these steps should be more than sufficient to pass the exam with a very good score.

---
>**_Course Link_**

**CKA:** https://www.udemy.com/course/certified-kubernetes-administrator-with-practice-tests/

**CKAD:** https://www.udemy.com/course/certified-kubernetes-application-developer/

---
>**_Practice Questions_**

**CKA**

https://github.com/alijahnas/CKA-practice-exercises

https://levelup.gitconnected.com/kubernetes-cka-example-questions-practical-challenge-86318d85b4d

**CKAD**

https://github.com/dgkanatsios/CKAD-exercises

https://github.com/bbachi/CKAD-Practice-Questions

https://codeburst.io/kubernetes-ckad-weekly-challenges-overview-and-tips-7282b36a2681

---

By dedicating few hours everyday, it's possible to get fully prepared within 1-6 weeks. Remember **PRACTICE PRACTICE PRACTICE**.

#### Exam Tips:

- Make most out of imperative commands, [kubectl cheatsheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/) is your saviour.
- At the beginning of exam, remember to [set auto-completions and kubectl alias](https://kubernetes.io/docs/reference/kubectl/cheatsheet/#kubectl-autocomplete), it will save a lot of time.
- Make use of bookmarks. [This is](https://gist.github.com/milindchawre/3558fabd7ee9ed72123d4be5b23f338c) what I used.
- Whenever you delete any kubernetes resources, make use of `--force` flag to delete faster without wait.
- During exam, make sure to switch to correct cluster context using the command given at the top of every question. Make sure to run it before attempting every question.
- If you are not familiar with any question or problem, just flag it and come back later.
- Get familiar with [kubernetes documentation](https://kubernetes.io/docs/home/), especially practice all the questions using your own [bookmarks](https://gist.github.com/milindchawre/3558fabd7ee9ed72123d4be5b23f338c). This will speed you up during exam, since you will know where to look what.
- Spend time depending on the weightage of the question. There is no point in spending 15 mins on a question with 2% weightage, skip those and come back later.
- During exam, sometimes you need to ssh to another node or change to root user. Beware of where you are all the time on bash terminal.
- Time management is very crucial, refer the section below on how to manage time wisely.

#### Exam terminal:
Its always good to know how your exam terminal looks like, go through [this official document](https://docs.linuxfoundation.org/tc-docs/certification/lf-candidate-handbook/exam-user-interface).
| ![exam-terminal](https://github.com/milindchawre/civo-k8s/raw/master/blog/cka-ckad/images/exam-terminal.png) |
|:--:|
| *exam terminal snap taken from linux foundation site* |


#### Bookmarks
You can use [bookmarks](https://gist.github.com/milindchawre/3558fabd7ee9ed72123d4be5b23f338c) during the exam, rather than searching in kubernetes docs. Trust me it saves lot of crucial exam time.
| ![cka-ckad-bookmarks](https://github.com/milindchawre/civo-k8s/raw/master/blog/cka-ckad/images/cka-ckad-bookmarks.png) |
|:--:|
| *bookmarks* |

#### Choose fast and optimal way to solve the question
There can be more than one way to accomplish a task in exam, use the one which is faster. For example, to create a pod make use of `kubectl run` command rather than writing kubernetes yaml manually, you will get scored irrespective of the way you used to accomplish this task. Please note that there is no any constraint to use a specific method to solve a problem, if you’re comfortable editing YAML in vim, go for it. Likewise, you can use kubectl generators, copy/paste docs from [kubernetes docs](https://kubernetes.io/docs/home/), or even edit a resource manually using `kubectl edit`. Refer [kubectl cheatsheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/), its very handy during exam.

#### Time management
You have 2hrs to solve 15-20 questions, so roughly 6-7mins per question. So time is very crucial. The order in which you solve the question is very important. This is what I followed while attempting every question:

| ![cka-ckad-flowchart](https://github.com/milindchawre/civo-k8s/raw/master/blog/cka-ckad/images/cka-ckad-flowchart.png) |
|:--:|
| *Exam Flow* |

- Read the question carefully. Try to understand what exactly need to be done, don't make any assumptions follow the facts mentioned in the question.
- If you decide to attempt the question, run the kubectl context command given at the top of the question before attempting it.
- If you think the question is very simple and can be done very quickly then just go for it, don't even look at the weightage of the question.
- If the question is too big with less/more weightage but little tricky and time consuming to solve then just flag the question and move on.
- While solving the question you got some error and you get stucked (or it took more time to fix it). Then just leave that question, flag it and move on. Flagging the question is very important, so that you can easily find and attempt it later.
- Once you solve the question, just do a quick verification of your answer. Remember to verify it quickly, taking too much time for verifcation after attempting each question will slow you down. Remember we have to save some time (atleast 10-15 mins) at the end of the exam to verify all the answers again at once in the end.
- Try to solve as much questions as possible in first half of the exam.
- If you are done with all the questions, then attempt all the flagged questions.

What if I don't follow above method and do this:
- Sequentially solving the questions.
- Getting stucked on some question (for more than 6-8mins).
- Solving tricky big questions with less weightage at the beginning, consuming lot of time.

This will put you under pressure with less time and more un-answered questions in hand. This will impact your score and overall exam result.

Everything here is the mind game, try to keep your calm.

#### Links to refer before exam
[Candidate Handbook](https://docs.linuxfoundation.org/tc-docs/certification/lf-candidate-handbook)
[Curriculum Overview](https://github.com/cncf/curriculum)
[Exam Tips](https://docs.linuxfoundation.org/tc-docs/certification/tips-cka-and-ckad)
[FAQ](https://docs.linuxfoundation.org/tc-docs/certification/faq-cka-ckad-cks)
[Certification and Confidentiality Agreement](https://docs.linuxfoundation.org/tc-docs/certification/lf-cert-agreement/)


#### Exam Day
- Try to schedule exam in the morning time, when your mind is fresh and free of other work related stress.
- Keep your room and desk clean.
- The exam duration is 2hrs, so carry a water bottle with you but it should be clear without any label.
- Make sure to join at least 15mins prior to exam time.
- Your exam is monitored online by a proctor, who is very strict about exam code of conduct.
- Exam begins with verification of identity and compliance with the rules. You need to activate your camera and share your screen. This process would take somewhere around 30mins.
- Don't cover your mouth, read out questions aloud, move away from your desk or center of your camera; its not allowed.
- All other instructions will be provided by proctor before the exam, strictly follow all those instructions.

#### Conclusion:
CKA and CKAD exams requires lot of hands on practice and skills. However, it is also one of the most rewarding experience. Even if you don't pass in first attempt, there is one free retake. Remember **PRACTICE PRACTICE PRACTICE**.

I hope this helps you. Good luck and feel free to ask any questions. Connect me on [Linkedin](https://www.linkedin.com/in/milind-chawre) and [Twitter](https://twitter.com/milindchawre).

