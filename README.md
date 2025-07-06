# # Node.js App Deployment with AWS CI/CD Pipeline

This project demonstrates how to containerize a Node.js application, push it to AWS CodeCommit, build and deploy the image using AWS CodeBuild, and finally run it on ECS Fargate with a Load Balancer — automating the full CI/CD workflow.



## Table of Contents

- [Project Overview](#project-overview)
- [Problem Statement](#problem-statement)
- [Architecture Diagram](#architecture-diagram)
- [Technologies Used](#technologies-used)
- [Features](#features)
- [Setup Instructions](#setup-instructions)
- [CI/CD Workflow](#cicd-workflow)
- [Testing](#testing)
- [Deployment](#deployment)
- [Folder Structure](#folder-structure)
- [Lessons Learned](#lessons-learned)
- [Future Improvements](#future-improvements)
- [License](#license)

## Project Overview

This project is designed to simulate a real-world DevOps CI/CD pipeline. A simple Node.js application is containerized and deployed using multiple AWS DevOps services. It showcases how teams can automate builds, version control, deployment, and scaling through ECS Fargate and load balancers.

## Problem Statement

In modern software development, deploying updates frequently with minimal downtime is crucial. Manual deployments are error-prone and time-consuming. This project aims to solve that by automating container builds and deployments to AWS ECS using CodePipeline.

##  Architecture Diagram

