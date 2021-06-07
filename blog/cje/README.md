
# Tips to ace Certified Jenkins Engineer 2021 exam

| ![cje-cert](https://github.com/milindchawre/civo-k8s/raw/master/blog/cje/images/cje-cert.png) |
|:--:|
| *CJE Certificate* |

I recently pass Certified Jenkins Engineer 2021 exam, scoring ~76%. This blog post talks about my overall exam experience and some tips.

***Note:** This blog is not about exam dumps, as it defeats the whole purpose of this exam. Also due to NDA, I can't reveal any questions asked in this exam. Though I can provide some pointers to help you with the exam preparation and to give you overall idea about the nature of this exam.*

### Jenkins Certification
[Jenkins Certification](https://www.cloudbees.com/jenkins/certification) is a professional-grade certification that demostrates your understanding of CI/CD concepts and DevOps best practices as well as the major features of Jenkins and, optionally, CloudBees Core.

Attaining certifies status means you possess the skills and hands-on experience necessary to implement and use [Jenkins](https://www.jenkins.io/). There are two certifications available:
- **Certified Jenkins Engineer (CJE):** This tests your proficiency with Jenkins and CloudBees Jenkins Distribution (CJD).
- **Certified CloudBees Jenkins Engineer (CCJE):** This tests your knowledge about enterprise version of Jenkins - [Cloudbees Core](https://www.cloudbees.com/).

### CJE or CCJE?
CJE exam is more suitable if you mainly work on open source version of jenkins. If you use cloud and enterprise version of Jenkins then go with CCJE exam.

### Registering for exam
The exam can be taken onsite at the [test centers](https://www.kryteriononline.com/Locate-Test-Center) or online from your home due to ongoing covid situation. In case you opt for onsite test make sure that the [test center is available in your locality](https://www.kryteriononline.com/Locate-Test-Center).

Once you decide, go and [register for the exam](https://www.webassessor.com/wa.do?page=enterCatalog&branding=CLOUDBEES&tabs=111).

| ![jenkins-exam-registration](https://github.com/milindchawre/civo-k8s/raw/master/blog/cje/images/jenkins-exam-registration.png) |
|:--:|
| *Jenkins exam registration* |

If you don't want to pay for exam, try to get free exam voucher from cloudbees which they offer every year if you attend the [DevOps World event](https://www.cloudbees.com/devops-world).

### Exam curriculum
To get overall idea, make sure to go through the [exam guide](https://standard.cbu.cloudbees.com/certification-guide-and-information).

#### - MCQs
The exam covers current version of Jenkins, CloudBees Jenkins Distribution (and CloudBees Core only for CCJE exam) and have multiple-choice questions to solve.

**CJE:** 60 multiple-choice question (about Jenkins and CloudBees Jenkins Distribution features) in 90 minutes

**CCJE:** 90 multiple-choice questions (60 questions about Jenkins and CloudBees Jenkins Distribution features and 30 additional questions about CloudBees Core features) in 120 minutes

#### - Exam topics
The exam mainly covers topics related to Jenkins Fundamentals, Jenkins Administration, Jenkins Build Technologies: Pipeline, Jenkins Build Technologies: Freestyle and  CloudBees Core Features (CCJE exam only).
| ![jenkins-curriculum](https://github.com/milindchawre/civo-k8s/raw/master/blog/cje/images/jenkins-curriculum.png) |
|:--:|
| *Jenkins exam curriculum* |

#### - Exam scoring
To pass the exam you need to correctly answer atleast 66% of questions. Each question can have a single or multiple correct answer. Each correct answer worth a single point. The question asked in exam clearly mention if there are multiple or single response to the question, you need to choose correct radiobox - for single answer question or tick correct checkbox - for multiple answer question (the question depicts if you need to choose 2 or 3 checkbox).

For more info, read the [official certification guide](https://standard.cbu.cloudbees.com/certification-guide-and-information).

### Exam Preparation:
This is what I referred:
- Cloudbees university course: https://standard.cbu.cloudbees.com/series/exam-preparation-certified-jenkins-engineer-cje
- Blogs: 
  * https://devopslearning.medium.com/my-road-to-certified-jenkins-engineer-807ae52409a0 
  * https://medium.com/@deepakr6242/top-ten-points-to-clear-certified-jenkins-engineer-in-a-month-for-free-a92d6d020a6e

The [cloudbees university cje course](https://standard.cbu.cloudbees.com/series/exam-preparation-certified-jenkins-engineer-cje) almost covers every topic asked in the exam, basically it has many pointers with appropriate references to respective jenkins documentation. The course also have hands-on labs to get acquainted with jenkins.

To follow the course: go through each concept, do practice labs and thoroughly refer relevant jenkins documentation.

The labs mentioned in the exam need jenkins setup, I find it difficult to setup vagrant + virtualbox on my windows workstation. So I quickly [setup jenkins on my civo kubernetes cluster](https://www.civo.com/learn/deploy-jenkins-from-the-civo-app-marketplace). I find it quiet convinient to pratice jenkins labs on my civo cluster. If you haven't signed up for civo, [do it now](https://www.civo.com/signup).


### Is theory enough or do I need hands-on?
Most of us assumes that MCQ based exam can be cracked by just going through the theory, but in case of jenkins you need to have some practical knowledge to ace this exam.

Basically the exam questions are of three kinds:
1. Straight forward question (you know the answer or not)
2. Question which need memorization (better to get some hands-on rather than memorizing)
3. In-depth question (you need more in-depth knowledge of specific concept to answer it)

Some hands-on practice helps in solving the 2nd and 3rd kind of questions.

### Exam Terminal
This is how your exam terminal looks like.
| ![jenkins-terminal](https://github.com/milindchawre/civo-k8s/raw/master/blog/cje/images/jenkins-terminal.png) |
|:--:|
| *Jenkins exam terminal* |

- ***Time Remaining:*** Shows how much time left to finish the exam.
- ***Question:*** Describes the question in detail.
- ***Review Question Checkbox:*** You can mark the question to review it later. If I'm doubtful about my answer, I opt to review it later.
- ***Answer / Options to Choose:*** This section contains multiple radiobox or checkbox options which you need to choose, to answer the question correctly.
- ***Test Aid:*** This section shows things that you can refer during the exam. For CJE exam none of these options are available.
- ***Back:*** To move to previous question.
- ***Next:*** To move to next question.
- ***Review All:*** Select this to review all the questions, usually done once you are finish answering all the questions and want to review doubtful questions.
- ***Submit Exam:*** Select this to finish the exam.

# Exam flow
You have 90 mins to solve 60 questions in CJE exam and 120 mins to solve 90 questions in CCJE exam. So roughly 1.5 min per question, so time management is very crucial. This is the process I followed while attempting each question:
| ![jenkins-exam-flow](https://github.com/milindchawre/civo-k8s/raw/master/blog/cje/images/jenkins-exam-flow.png) |
|:--:|
| *Jenkins exam flow* |

# Exam Day
Assuming the exam is online proctored.

Before exam day:
- Refer the [kryterion guide](https://kryterion.force.com/support/s/topic/0TO1W000000I5h3WAC/online-proctoring?language=en_US) before attempting the exam.
- Go through [exam FAQ page](https://support.cloudbees.com/hc/en-us/articles/360046012631-CloudBees-Training-and-Certification-FAQ)
- Make sure your machine pass the [system check](http://www.kryteriononline.com/systemcheck).

On exam day:
- Make sure to appear atleast 15 mins before the exam.
- Keep your personal documents handy, you might need it for ID verification in case of online proctored exam. Though in my case, the system don't asked for any ID, but its good to keep it handy.
- Make sure your room is quiet and your desk is clear.
- In online proctored exam, the exam launch button appears 10 mins before the scheduled exam time. Make sure you are ready prior that.

# After the exam
As soon as you finish the exam, the result is shown on your screen. It shows overall percentage you got and your score in each section. If you fail in exam, the result helps in understanding which section you should focus more on. Remember there is no free second attempt, you need register and pay for the exam again.
<My exam result pic>
| ![cje-score](https://github.com/milindchawre/civo-k8s/raw/master/blog/cje/images/cje-score.png) |
|:--:|
| *CJE exam score* |

# Conclusion
CJE and CCJE exam requires some basic knowledge about CI/CD concepts and some hands-on practice of jenkins. However, it is also one of the most rewarding experiences.

I hope this guide helps you in preparing for the exam. Good luck and feel free to ask any questions. You can connect with me on the Civo community Slack, [Linkedin](https://www.linkedin.com/in/milind-chawre) and [Twitter](https://twitter.com/milindchawre).

