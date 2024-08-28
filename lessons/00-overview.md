# Overview 

Welcome to CS 265: Code Generation and Optimization!

See the [README](../README.md) for logistics, policies, and course schedule.

**Caveat lector**:
 This course is under construction.
 That means that this document is subject to change!
 That also means you are welcome to provide feedback to me about the course.
 You may also provide direct edits to course materials (either here or in other course repos)
  via pull requests if you feel so inclined, but it is not at all required.

## Course Goals

This is an advanced compilers class targeted at students who have experience building some level of compilers or interpreters.
We will focus on the "mid-end" of the compiler, which operates over an intermediate representation (IR) of the program.
The lines between front/mid/back-end don't have to be totally stark.
A compiler may have more than one IR (sometimes many, see the [Nanopass Compiler Framework](https://nanopass.org/)).
For the purpose of this class, let's define the "ends" of a compiler as follows.

- Front-end
	- Responsible for ingesting surface syntax, processing it, and translating it into an intermediate representation suitable for analysis and optimization
	- Examples: lexing, parsing, type checking, macro/template processing, elaborating language features into a reduced set of features.
- Mid-end
	- Many modern compilers include a "mid-end" portion of the compiler where analysis and optimization is focused.
	- The goal of the mid-end is to present a reduced surface area to make compiler tasks easier to work with.
	- Mid-ends are typically agnostic to details about the target machine.
- Back-end
	- The back-end is responsible for lowering the compiler's intermediate representation into something the target machine can understand. 
	- This layer is typically per target machine, so it will refer to specifics about the target machine architecture.
	- Examples: instruction selection, legalization, register allocation, instruction scheduling

We will focus on the mid-end, 
 and we'll touch on some aspects of the backend. 
Compiler front-ends are not really going to be covered in this course.
You are free to incorporate any part of the compiler in your course project.

## Topics Overview

Here are some of the planned topics for the course.

## Representing Programs

## Basic Optimizations

"optimizations"

## Transformation

## Static Analysis

## Loop Optimizations

## "Exotic" Hardware

## Potpourri






