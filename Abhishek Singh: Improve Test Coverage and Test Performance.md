# Improve Test Coverage and Test Performance

## Abstract

This project focuses to improve yt's test framework. At present, yt's code coverage is only 26% and the test runtime is approximately 45 minutes. The aim of this project is to increase code coverage and reduce test runtime.

First, I propose the use of [Coveralls](https://coveralls.io/), which is tool to monitor the code coverage and is free for open source repositories. This would not only help in analyzing the key areas that need immediate attention for coverage but will also help in maintaining higher code coverage for future developments.

Next, I will enhance the yt test suite by writing test cases for the flows that are not being tested currently. Runtime of tests could be improved by optimizing (or reducing) answer testing and image comparisons tests for visualization and volume rendering modules. This project also focuses on streamlining test cases for different geometries and data styles.

## Technical Details

### Coveralls Integration

Coveralls is a tool to analyze code coverage with each pull request. Thus, the main advantage of this tool is that with each pull request one can know how much of the new code is being been tested.

I created a sample [github project](https://github.com/git-abhishek/Poc-Coverage) to show Coveralls integration. Initially, I created two functions ``add(a,b)`` and ``sub(a,b)`` with unit test case for only ``add`` function. Due to this, the code coverage came out to be 86%. Then I created a PR to add the test case for ``sub`` function. The affect of this PR could be easily analyzed at the [coveralls build page](https://coveralls.io/builds/16036140).

![Fig 1. Coveralls build page for the PR](https://github.com/git-abhishek/Poc-Coverage/blob/master/image/covealls%20build%20of%20PR.PNG "Fig 1. Coveralls build page for the PR")

In addition, we also get a small summary at the [PR page](https://github.com/git-abhishek/Poc-Coverage/pull/2) depicting the overall code coverage owing to these changes. Using this we can easily track if the new changes are being properly tested or not.

![Fig 2. Coveralls summary at the PR page](https://github.com/git-abhishek/Poc-Coverage/blob/master/image/Coverage%20Summary%20PR.PNG "Fig 2. Coveralls summary at the PR page")

### Current Code Coverage Analysis
### Improving Code Coverage
### Improving Test Runtime
[code coverage](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage/index.html)
[code coverage with branching](https://cdn.rawgit.com/git-abhishek/Poc-Coverage/cb7e62e4/coverage/code_coverage_branching/index.html)

Long description of the project. **Must** include all technical details of the
projects like libraries involved.

Here it is important to show if you had previous conversations with your
mentors. You can show relevant pieces of code that you want to change. You can
link to literature you used during the research.

## Schedule of Deliverables

Here should come a list of your milestones. This list is a start based on the
difference phases of GSoC. Use it as a start. You can/should add more details
for each phase by breaking it down into weeks or set specific targets for each
phase. Each target should be split into sub task with a time estimate, [work
breakdown structures][wbs] are helpful here.

### **Community Bonding Period**

This phase is to get to know the community better. Check that your build
environment is setup. This time should also be used to discuss your project in
more detail with the community and in general introduce it. 

*Note:* We require you to write regular blog posts. Now is a good time to make
sure your blog works and send us the link.

### **Phase 1**

Delieverables

### **Phase 2**

Delieverables

### **Final Week**

At this stage you should finish up your project. At this stage you should make
sure that you have code submitted to your organization. Our criteria to mark
your project as a success is to submit code before the end of GSoC.

## Development Experience

I have 3 years 3 months of work experience as a software developer engineer at various technology [companies](#3-work-experience). During these years, I worked on many different technologies ranging from Java, Python, C/C++/C#, ASP.NET, SQL to JavaScript, jQuery, HTML/CSS and learnt many frameworks. This experience has not only made me a better developer but also taught me to reach for coding excellence. It exposed me to the skills that make a developer complete like working in global teams, timely delivery of production code in large codebases and taking product ownership. Thus, I feel I am competent enough to work on this project.

As part of my coursework in [Neural Networks](https://compsci682.github.io/) I implemented deep learning library in python. For the course [Applied Infromation Retrieval](http://ciir.cs.umass.edu/~dfisher/cs546/syllabus.html), I created a toy search engine in Java that could efficiently index the corpus and return ranked query results. This semester in [AI](http://rbr.cs.umass.edu/shlomo/classes/683/index.html) I am implementing search algorithms like A*, constraint satisfaction problems, minimax adverserial search and others. In [Intelligent Visual Computing](https://people.cs.umass.edu/~kalo/courses/visual_computing/index.html) I am writing Matlab code for 3D shape reconstruction, deepnet eye detector and marching cubes. Furthermore, I am implementing a simulator based [robot](http://www-robotics.cs.umass.edu/~grupen/603/handouts/syllabus.html) in C with 9 degrees of freedom. Since all these projects are forbidden to be shared publicly, I do not have them on GitHub. (I will be required to take professors' permission, in case I need to send it privately to the reviewers.)    

yt is my first open source community and thus I do not have any contributions that are publicly available. However, I have made 2 PRs till now to fix issues [#1680](https://github.com/yt-project/yt/issues/1680) and [#1599](https://github.com/yt-project/yt/pull/1705).

## Other Experiences


## Why this project?

For any software product with increasing userbase, stability and reliability becomes utmost. Furthermore, no organization wants to ship more features at the cost of robustness. This makes effective test suite one of the most important backbone of any product. Better testing framework not only leads to early bug detection but also contributes to rapid code development. At present the code coverage of yt is only one-third and there is a lot of scope to improve the runtime which makes me highly interested in this project.

Increasing code coverage at the same time reducing proportional test runtime makes this an interesting problem. This demands higly optimal test code and intelligent handcrafting of test cases. This makes it a challenging problem requiring extensive analysis and well-thought-out solution.

Furthermore, this project touches the entire codebase of yt. It would not only help me understand yt better but would also make me competent to keep contributing in future. I believe when one knows the end-to-end of a system, then one can think of innovative and polished solutions of the shortcomings in the system.

Given an opportunity to work on this project, I am confident with my possessed skills that I will be able to enhance yt that will make its developer more confident and at the same time increase the penchant of its userbase.

## Appendix
### 1. Personal Details
  * Name: Abhishek Singh
  * Current Degree Program: Master's in Computer Science
  * Current University: [University of Massachusetts, Amherst](https://www.umass.edu/)
  * Past Degree Program: Master's in Mathematics, Bachelor of Engineering in Computer Science
  * Past University: [Birla Institute of Technology and Science, Pilani](http://www.bits-pilani.ac.in/pilani/)
  * Phone: +1 413-824-1385
  * Email/Google Hangout: abhisheksing@umass.edu
  * GitHub: https://github.com/git-abhishek
  * LinkedIn: https://www.linkedin.com/in/asingh690/
  * [Resume](https://github.com/git-abhishek/gsoc-2018/blob/master/Abhishek_Singh_Resume.pdf)
  
### 2. Relevant Programming Skills
  * Python, Java, C/C++
  * Git and GitHub
  * Cython (Beginner)
  
### 3. Work Experience
  * Software Robotics Corp. (Junior Platform Engineer, 4 months)
  * Citi Bank (Applicaiton Developer Supervisor, 30 months)
  * Amazon (SDE Intern, 5 months)
   
### 4. Summer'18 Commitment
  * Except for GSoc, I do not have any other engagements for the summer. I will be working fulltime (40 hrs/week) on this project.
  
[wbs]: https://en.wikipedia.org/wiki/Work_breakdown_structure
