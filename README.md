# Search 2D

![search2d-concept](https://github.com/buri83/search2d/blob/main/doc/search2d-concept.jpg)

**This package is under development.**

Quickly search for entities in the 2D world with a rectangular range query.

## Install

```bash
npm i search2d
```

## Example

```typescript
import { EntityPosition, Search2D, SearchableEntity } from "search2d";

type ExampleEntityObject = {
    name: string; // Additional field
} & SearchableEntity;

// If you want to use a class, write the following
class ExampleEntityClass implements SearchableEntity {
    constructor(
        readonly id: string, // Required
        readonly position: EntityPosition, // Required
        readonly name: string, // Additional field
    ) {}
}

// Specify field height and width
// Entity's position range: 0 <= y <= height,  0 <= x <= width
const search = new Search2D<ExampleEntityObject>({ height: 100, width: 100 });

const entity: ExampleEntityObject = {
    id: "001", // id must be unique
    position: new EntityPosition({ x: 10, y: 20 }),
    name: "buri",
};
// Register entity to search
search.register(entity);

// Search by query
const result = search.search({
    position: {
        xFrom: 10,
        yFrom: 10,
        xTo: 20,
        yTo: 20,
    },
});
console.log(result);
// {
//   entities: [ { id: '001', position: [EntityPosition], name: 'buri' } ]
// }

// Move entity position (Change x, y at the same time, it is faster)
entity.position.set({ x: 15, y: 15 });

// x, y can also be set individually
entity.position.x = 15;
entity.position.y = 15;

// You can deregister entity
search.deregister(entity);

// Deregister all entities before disposing search2D instance to prevent memory leak.
search.deregisterAll();
```

# Benchmark

Search2D is about 10x faster than NaiveSearch. (10k entities)  
benchmark code is [here](https://github.com/buri83/search2d/blob/main/src/tests/benchmark.spec.ts)

|                | NaiveSearch | Search2D |
|----------------|-------------|----------|
| Registration   | 3ms         | 17ms     |
| Deregistration | 3ms         | 9ms      |
| Search         | 1706ms      | 174ms    | 


```typescript
// NaiveSearch (https://github.com/buri83/search2d/blob/main/src/naiveSearch.ts)
const entities: T[] = [];
for (const entity of this.entities.values()) {
    const isContained =
        query.position.xFrom <= entity.position.x &&
        entity.position.x <= query.position.xTo &&
        query.position.yFrom <= entity.position.y &&
        entity.position.y <= query.position.yTo;
    if (isContained) {
        entities.push(entity);
    }
}
```
