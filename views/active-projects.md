name: Active Projects
filters:
  all:
  - field: type
    op: equals
    value: Project
  - field: Status
    op: equals
    value: Active
  - any:
    - field: status
      op: equals
      value: active
    - field: date
      op: after
      value: in 1 week
