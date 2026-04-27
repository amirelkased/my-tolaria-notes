---
type: Spring Data JPA
---
# Cascade vs OrphanRemoval

### Cascade — Propagate Operations Parent → Child

```text
What you do to parent → happens to child
```

| Type      | Trigger                        |
| --------- | ------------------------------ |
| `PERSIST` | save parent → save child       |
| `MERGE`   | update parent → update child   |
| `REMOVE`  | delete parent → delete child   |
| `REFRESH` | refresh parent → refresh child |
| `DETACH`  | detach parent → detach child   |
| `ALL`     | all of the above               |

***

### `orphanRemoval` — Remove Disconnected Children

```text
Child removed from parent's collection → deleted from DB
```

```java
order.getItems().remove(item); // orphanRemoval=true → deleted from DB ✅
order.getItems().clear();      // all items deleted ✅
order.setProfile(null);        // @OneToOne — profile deleted ✅
```

***

### The Key Difference

```text
CascadeType.REMOVE  → parent deleted → children deleted
orphanRemoval       → child removed from collection → child deleted
                      parent deleted → children deleted (also!)
```

***

### Where to Put Them

```java
// ✅ Always on the PARENT (mappedBy) side
@OneToMany(mappedBy = "order", cascade = CascadeType.ALL, orphanRemoval = true)
private List<OrderItem> items;
```

***

### Per Relationship

|                 | `@OneToOne` | `@OneToMany` | `@ManyToMany`             |
| --------------- | ----------- | ------------ | ------------------------- |
| Safe cascade    | ALL         | ALL          | PERSIST, MERGE only       |
| `orphanRemoval` | ✅           | ✅            | ❌ not supported           |
| `REMOVE` danger | ✅ safe      | ✅ safe       | ❌ deletes shared entities |

***

### One Rule to Remember

> *"Cascade = parent operations flow down. orphanRemoval = disconnected children die. Never use REMOVE or ALL on @ManyToMany."*
