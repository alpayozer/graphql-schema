# Types

type Employee {
  employee_id: ID!
  first_name: String!
  last_name: String!
  email: String!
  position: String
  status: EmployeeStatus!
  tasks: [EmployeeTask!]  # Relation: Employee → Tasks
}

enum EmployeeStatus {
  ACTIVE
  ONBOARDING
  OFFBOARDING
  INACTIVE
}

type Template {
  template_id: ID!
  template_name: String!
  type: TemplateType!
  created_by: User!
  tasks: [TemplateTask!]  # Relation: Template → TemplateTasks
}

enum TemplateType {
  ONBOARDING
  OFFBOARDING
}

type TemplateTask {
  task_id: ID!
  template: Template!
  task_name: String!
  assignee_role: String!
  relative_timing: String! # e.g., "Day 1", "-3 days"
}

type EmployeeTask {
  emp_task_id: ID!
  employee: Employee!
  template_task: TemplateTask!
  assignee: User!
  due_date: String!
  status: TaskStatus!
}

enum TaskStatus {
  TODO
  IN_PROGRESS
  DONE
}

type User {
  user_id: ID!
  name: String!
  email: String!
  role: UserRole!
}

enum UserRole {
  HR
  MANAGER
  EMPLOYEE
}

type Notification {
  notification_id: ID!
  user: User!
  emp_task: EmployeeTask!
  message: String!
  created_at: String!
}

# Queries

type Query {
  # Employees
  employees(status: EmployeeStatus): [Employee!]
  employee(id: ID!): Employee

  # Templates
  templates(type: TemplateType): [Template!]
  template(id: ID!): Template

  # Tasks
  employeeTasks(employee_id: ID!): [EmployeeTask!]
  task(id: ID!): EmployeeTask

  # Users
  users(role: UserRole): [User!]
  user(id: ID!): User

  # Notifications
  notifications(user_id: ID!): [Notification!]
}

# Mutations

type Mutation {
  # Employees
  createEmployee(first_name: String!, last_name: String!, email: String!, position: String): Employee
  updateEmployee(id: ID!, status: EmployeeStatus, position: String): Employee
  deleteEmployee(id: ID!): Boolean

  # Templates
  createTemplate(template_name: String!, type: TemplateType!, created_by: ID!): Template
  addTemplateTask(template_id: ID!, task_name: String!, assignee_role: String!, relative_timing: String!): TemplateTask

  # Employee Tasks
  assignTasksToEmployee(employee_id: ID!, template_id: ID!): [EmployeeTask!] # Generates tasks from template
  updateTaskStatus(emp_task_id: ID!, status: TaskStatus!): EmployeeTask

  # Users
  createUser(name: String!, email: String!, role: UserRole!): User
  updateUserRole(user_id: ID!, role: UserRole!): User

  # Notifications
  markNotificationRead(notification_id: ID!): Notification
}

# Subscriptions

type Subscription {
  taskAssigned(user_id: ID!): EmployeeTask
  taskUpdated(employee_id: ID!): EmployeeTask
  notificationReceived(user_id: ID!): Notification
}
