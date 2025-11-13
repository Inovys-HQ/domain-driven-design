# @inovys/domain-driven-design

Lightweight, framework-agnostic library providing tactical building blocks for **Domain-Driven Design (DDD)** in TypeScript.

[![npm](https://img.shields.io/npm/v/@inovys/domain-driven-design)](https://www.npmjs.com/package/@inovys/domain-driven-design)

## Installation

```bash
npm install @inovys/domain-driven-design
```

## Quick Demo

This demo shows the core tactical building blocks in action: Entities, Aggregates, and Domain Events.

```typescript
import {
  Entity,
  AggregateRoot,
  DomainEvent,
} from "@inovys/domain-driven-design";

// ------------------------
// 1. Define an Entity
// ------------------------
class User extends Entity<{ email: string }> {
  static create(email: string) {
    return new User({ properties: { email } });
  }
}

// Create a user
const user = User.create("alice@example.com");
console.log(user.id, user.properties.email);

// ------------------------
// 2. Define an Aggregate
// ------------------------
class AccountCreated extends DomainEvent<{ accountId: string }> {}

class Account extends AggregateRoot<{ balance: number; userId: string }> {
  static create(userId: string) {
    const account = new Account({ properties: { balance: 0, userId } });

    // Record a domain event
    account.commit(new AccountCreated({ payload: { accountId: account.id } }));

    return account;
  }

  deposit(amount: number) {
    this._properties.balance += amount;
  }
}

// Create an account aggregate
const account = Account.create(user.id);
account.deposit(100);
console.log(account.snapshot());

// Pull recorded events
const events = account.pullDomainEvents();
console.log(events);
```

> For a full working example using these patterns, check out the [NestJS Clean Boilerplate](https://github.com/Gi-jutsu/nestjs-clean-boilerplate)
