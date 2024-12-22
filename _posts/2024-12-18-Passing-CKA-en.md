---
layout: post
title: Reflections After Passing the CKA Exam
tags: k8s en certificate
---
(Translated by ChatGPT, from the [original post](https://rzhi.xyz/2024-12-17/Passing-CKA-zh) ) 

On December 17, I finally fulfilled my resolution from the beginning of the year by passing the CKA exam. With a score of 92, it was a decent result, in line with my expectations.

This post is to document my experiences before and during the exam.

# Exam Preparation

Let me start by describing my background for reference, especially for those whose situations might differ from mine. 

I've been working in the DevOps field for five years, and I am quite familiar with Linux environments. Basic command-line operations are comfortable to me. While I'm not an expert in Docker, I have a solid understanding of its fundamentals. My work doesn’t involves Kubernetes on a daily basis—I mostly handle upgrades and maintenance for pre-existing clusters—so I only had a superficial grasp of Kubernetes before taking courses. Troubleshooting is one of my strengths, and I genuinely enjoy it.

Given these prerequisites, the courses and exercises I completed were enough to prepare thoroughly for the CKA exam.

## Courses and Practice
I mainly relied on the Udemy course [Certified Kubernetes Administrator (CKA) with Practice Tests](https://www.udemy.com/course/certified-kubernetes-administrator-with-practice-tests/) and the practice labs on ACloudGuru. Additionally, the sample exams provided through killer.sh after registering for the test were an excellent practice.

The Udemy course is outstanding—well-structured, progressive, and accommodating of different entry levels. It includes a lot of foundational content, but you’ll probably need some basic Linux knowledge to follow along. So if you're entirely new to Linux, I recommend starting with introductory Linux tutorials first. The course also comes with numerous labs hosted on KodeKloud, which provides extremely important hand-on exercises and shouldn’t be skipped.

At the end of the course, there’s a half-hour "lightning exam" to help you get your feet wet, as well as three one-hour sample exams. I noticed these three exams are arranged in increasing order of difficulty, although this isn’t explicitly mentioned in the course.

I also tried part of ACloudGuru’s CKA course, but I found it less helpful, so I wouldn’t recommend it. However, apart from the course, ACloudGuru also provide 10 practice scenarios, each with a different focus. The difficulty feels similar to the actual exam, and they’re worth trying. I personally felt that ACloudGuru’s lab environment was more stable compared to KodeKloud’s which gives a better experience.

killer.sh’s practice exams are significantly harder than the real exam. They consist of complex, large-scale problems that cover a wide range of topics. If I remember correctly, there are 25 questions in total, which are almost impossible to finish within the normal exam duration which is two hours. However, the test environment is accessible for 36 hours, giving you plenty of time to complete the questions and experiment. Each question comes with detailed explanations, so even if you can’t solve a problem, you should follow the solution step by step. Also, killer.sh’s test environment closely resembles the real exam, making it a must-try resource.

A tip for practicing: don’t save the hardest sample exams right before the actual one—they can crush your confidence if you struggle with them. I recommend completing killer.sh’s exercises at least a week before your exam so you have time to review your mistakes. KodeKloud’s simpler exercises can be reserved for the days leading up to the exam to keep your skills fresh.

Additionally, while practicing, pay attention to not only the commonly used commands but also the keywords that make searching the official CKA documentation more efficient. For instance, searching "Pods to Nodes" can help you find how to schedule pods to specific nodes, and "rbac" retrieves resources related to role access. These search strategies are incredibly useful during the exam. You can refer to my earlier post, [Notes from Sample Exam](https://rzhi.xyz/2024-12-09/CKA-preparaion), which contains notes I prepared for my own review. Although it’s written only for myself, not for any other specific audience, you might still find some useful tips there.

## Environment Setup
There are plenty of online guides about the exam process, so I won’t repeat them here. However, my environment preparation was lacking, and I almost couldn’t take the test. Here are the pitfalls I encountered.

I have a desktop PC with good specs, which I usually use for gaming and personal projects. This machine didn’t initially have a microphone or webcam, so I borrowed a cheap webcam with a built-in mic for the exam. Since someone else had successfully used it for the CKA, I didn’t worry about its poor quality. Unfortunately, the proctor rejected it, saying it was too blurry when checking my ID. They told me that I had to switch to my phone’s camera, but I couldn’t figure out how to configure it in time. Luckily, I had my MacBook as a backup, so I switched to it and reconnected to the exam environment. If I hadn’t had the laptop or started setting up half an hour early, I might have missed the exam entirely (being over 15 minutes late forfeits the exam). Thinking back, it’s a chilling thought.

Another issue was with the three practice tests provided by the PSI Secure Browser. These tests help you get familiar with the layout and basic operations of the browser. For some reason, I couldn’t find the entry point for these tests before the exam. I only found them after clicking the button to start the exam half an hour before my scheduled time, but by then, the practice tests had expired. As a result, I started the exam without ever practicing in the browser. This wasn’t a major problem, but I couldn’t figure out how to copy and paste in the terminal using keyboard shortcuts, so I had to rely on clicking the menu options or retype everything. It wasn’t a significant obstacle, but it did ruin my mood a bit. Don’t be like me—read the instructions carefully and check all emails (until now, I still don’t know what I missed XD).

Other than that, everything went smoothly.

# Exam Experience
I’ll skip over easily accessible information, as they can be found on Internet.

One thing to note is that the remote desktop environment during the exam can experience slight delays or occasional lag. Make sure you have a stable internet connection. I’m not sure about the process for reconnecting if you lose connection, but it would definitely disrupt your focus and time management. Avoid this scenario at all costs.

The exam itself wasn’t too difficult. The instructions were clear. Since I work in an English-speaking environment, I took the exam in English and didn’t encounter any issues understanding the prompts. Despite the last-minute device switch and extra time spent getting used to the environment, I finished all the questions with half an hour to spare, leaving enough time to review everything. Time management was not an issue.

# Conclusion
I found the CKA exam quite enjoyable. It’s hands-on, and the difficulty is moderate. I didn’t dedicate excessive energy to preparation—just two months of studying and practicing for a few hours each week. I was also able to spend a week of work time fully focused on preparing for the exam. Since I didn’t push myself too hard, the preparation process didn’t feel exhausting.

That’s all I can think of. I hope this post is helpful to anyone preparing for the CKA. Best of luck with your exam!

