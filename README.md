# Activity 25: SOLID PRINCIPLES in Angular

## Single-responsibility principle

The general idea with this principle is that each class should have one responsibility. In an Angular context, whether that class is a component, directive, pipe, service, or anything else - it should have one responsibility.

Determining what a “responsibility” is, is part of the fun vagueness of these principles that can make them hard to apply. What is a “responsibility”? For example, imagine a service that manages the data/state for task in an application. That service might:

Get task

Add task

Delete task

    // task.service.ts
    import { Injectable } from '@angular/core';
  
    @Injectable({
      providedIn: 'root'
    })
    export class TaskService {
      private tasks: string[] = [];
  
    // TaskService has only one responsibility: managing tasks.
    getTasks(): string[] {
      return this.tasks;
    }
  
    addTask(task: string): void {
      this.tasks.push(task);
    }
  
    deleteTask(task: string): void {
      this.tasks = this.tasks.filter(t => t !== task);
    }
    }

In this service, the TaskService class has only one responsibility: managing the list of tasks (fetching, adding, deleting tasks). It does not handle anything related to the user interface or other business logic.

## Open-closed principle

This is another principle that applies quite well to Angular. This principle states that an entity should be open for extension, but closed for modification. In other words, when creating new features we should incorporate/extend/build on top of existing entities, not modify those existing entities.

Let's assume we have a notification service that sends messages via different channels (e.g., email or SMS). The base service is open for extension, meaning we can add new notification methods without modifying the existing code.

    export abstract class NotificationService {
      abstract sendMessage(message: string): void;
    }

This is the abstract base class. It doesn't define how to send a message, so it's open for extension by any class that extends it.

    import { Injectable } from '@angular/core';
    import { NotificationService } from './notification.service';
  
    @Injectable({
      providedIn: 'root'
    })
    export class EmailNotificationService extends NotificationService {
      sendMessage(message: string): void {
        console.log(`Sending email with message: ${message}`);
      }
    }

In this setup, the NotificationService base class is closed for modification, but you can extend it by creating new notification services (like EmailNotificationService)

## Liskov Substitution Principle

We will incorporate this concept somewhat when we look at the dependency inversion principle in a moment, but this is not a principle that is generally particularly prevalent in Angular development.

The basic idea is that a sub-class should be able to replace a base class and the program should still function. With OOP, we can extend classes, e.g:
    
    export class ButtonComponent {}
    export class SpecialButtonComponent extends ButtonComponent {}

The idea with the Liskov Substitution principle is that we should be able to use our SpecialButtonComponent in place of the ButtonComponent and everything will still function. Our SpecialButtonComponent does everything the ButtonComponent does because it extends it, so it should be able to be used in place of the ButtonComponent.

    @Component({
        selector: 'app-button',
        template: `
        <button>Hi</button>
    `
    })
    export class ButtonComponent {

    someClassMember: boolean;

    someMethod(){

    }
    }

    @Component({
        selector: 'app-special-button',
        template: ``
    })
    export class SpecialButtonComponent extends ButtonComponent {

    anotherMethod(){

    }
    }

## Interface Segregation principle

The interface segregation principle states that: Clients should not be forced to depend upon interfaces that they do not use.

This is something that is relevant to Angular, let’s consider an example. We might have an application that handles displaying Articles. The specific entity here doesn’t really matter, the main point is that we are displaying something using the master/detail pattern.

We might have an interface representing articles that looks like this:

    interface Article {
        id: number;
        title: string;
        summary: string;
        author: string;
        datePublished: string;
        dateUpdated: string;
        content: string;    
    }

We might also have two different components that deal with displaying articles. We might have one component that handles displaying a summary of the article in a list, and we might have one component that handles displaying the full details and content of an article.

Now, let’s say our ArticleSummary component displays the following details:

title

summary

author

dateUpdated

If we have our ArticleSummary component use the Article interface we are violating the interface segregation principle. It is depending on an interface that contains properties it does not need. Our component that displays the full details of an article would use all these fields.

To fix this, and adhere to the interface segregation principle, we might do something like this instead:

    interface Article {
        id: number;
        title: string;
        summary: string;
        author: string;
        dateUpdated: string;
    }

    interface ArticleDetails extends Article {
        datePublished: string;
        content: string;   
    }

ow our summary component can use the Article interface, and the full details component can use the ArticleDetails interface, and neither depend on an interface with properties they don’t use. Exactly how we break these interfaces up might depend on how we intend to structure our application.

## Dependency Inversion

principle states: depend upon abstractions, not concretions.

In Angular, this principle is naturally supported through Dependency Injection (DI). By injecting dependencies via interfaces or abstract classes, you ensure that high-level modules rely on abstractions rather than concrete implementations, making the system more flexible and testable.

It is easier to understand this if you know what abstract classes and methods are. An abstract class or method defines the idea or “shape” of a class or method. For example, I might have an abstract math class that looks like this:

    abstract class Math {
        abstract add(x: number, y: number): number;
        abstract multiply(x: number, y: number): number;
    }

    class MyMath implements Math {
        add(x: number, y: number) {
            return x - y;
        }

    multiply(x: number, y: number) {
        return x / y;
    }
    }

The idea with this principle is that our application would depend on Math which is just an abstraction. We could then provide whatever implementation of that we want.

Reference: https://ionicstart.com/modules/basics-application-architecture/2/
