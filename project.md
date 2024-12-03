# CS 265: Final Project

This course features a project component.

You may do the project individually or in groups of 2-3 people.
Unlike the reflection assignments, 
 you should submit a single project report for the group.

You cannot use late days on the project.
If you think you will need an extension, please talk to me as soon as possible.

See the [schedule](./README.md#schedule) for due dates.

**If you are in a group**,
 have one person submit the proposal/check-in/report and list the group members in the proposal.
The others should just submit a text entry saying that they are in a group with that person.

## Project Proposals

The first part of the project is to write a proposal.

The purpose of the proposal is to help you scope out project 
 that is both interesting and feasible
 to complete in the time allotted.

The proposal should be at least 2-3 pages long and should be submitted as a PDF on bCourses.

The proposal should include the following sections:

- Intro
    - What are you doing?
    - What is your goal?
    - Why?
- Background
    - What do you already know or need to learn to do this project?
    - What pieces of infrastructure will you need? Bril? LLVM? Doop?
        - Are you already familiar with these? Or will you need to learn them?
    - What parts are already done?
- Approach
    - How will you accomplish the things that need to be done?
    - What software will you need to build? Algorithms to implement? Papers to read?
- Evaluation plan
    - How will you know if you've succeeded?
    - What will you measure?

I will broadly accept projects that are related to any part of compilation, not just the middle-end that we focused on in class.

Here are some (non-exhaustive) categories that I expect to see projects in:
1. Expanding your Bril compiler
    - Pick a new optimization or class of optimzations to implement and measure
        - I expect this will be the most common project, and that's great!
    - Expand the Bril infrastructure in some way
        - Generate Bril from a new source language
        - Add new IR features (parallelism, virtual functions, etc.)
        - Implement a backend to a real or virtual architecture
2. Any of the above in some other compiler infrastructure
    - probably only do this if you already have experience with the infrastructure
3. Connecting up with your current research project / hobby project
    - If you're already working on a project that involves compilation
    - Still follow the project guidelines above re: goal setting and evaluation
4. Survey paper
    - If you're not interested in implementing something, you can write a survey paper on a topic in compilation
    - Still follow the project guidelines above re: goal setting
    - "Evaluation" will be a more nuanced reflection on how your report compares with the state of the art. What doesn't it cover?

Looking for ideas, come chat with me!
For more inspiration, 
 see what students in the similar [CS 6120](https://www.cs.cornell.edu/courses/cs6120/2023fa/blog/) course at Cornell did in that instance or others.

## Project Check-ins

This is a ~1 page report submitted to bCourses to update me on your progress. It should answer the following questions:
1. What have you done so far to make progress towards your stated project goals?
2. Do you need to modify your project goals? If so, how?
3. What do you plan to do next?
    - If you're feeling on track, then let me know what's still to be done.
    - If you need to change your project goals, write how you plan to accomplish the new goals.

## Project Report

The project report should be 4-6(ish) pages in length, and should be submitted on bCourses.
Ultimately, the content of the report is flexible, but it should be self-contained
 (not relying on the reader to have read your proposal or check-in).
The report should focus on evaluation, to the extent that it makes sense for your project.
Include graphs, tables, and other visualizations as needed.
If you plan to continue work on the project (not required, but some projects are part of a larger research agenda),
 include a section on future work.

### Making Projects Public

You may **optionally** choose to make your project public.
If you do this, I will link to your project from the course website, and post to social media saying "look at these cool projects!".
To do this:
1. Submit only a URL to your project report on bCourses. The URL should point to a public website (github, personal website, etc.) where your project report is hosted.
2. Include a comment in the submission that says "I would like my project to be public."