# Data Normalization and Entity-Relationship Diagramming

An assignment to normalize the structure of data and establish a set of Entity-Relationship Diagrams for the data.

## 1. Original data set

| assignment_id | student_id | due_date | professor | assignment_topic                | classroom | grade | relevant_reading    | professor_email   |
| :------------ | :--------- | :------- | :-------- | :------------------------------ | :-------- | :---- | :------------------ | :---------------- |
| 1             | 1          | 23.02.21 | Melvin    | Data normalization              | WWH 101   | 80    | Deumlich Chapter 3  | l.melvin@foo.edu  |
| 2             | 7          | 18.11.21 | Logston   | Single table queries            | 60FA 314  | 25    | Dümmlers Chapter 11 | e.logston@foo.edu |
| 1             | 4          | 23.02.21 | Melvin    | Data normalization              | WWH 101   | 75    | Deumlich Chapter 3  | l.melvin@foo.edu  |
| 5             | 2          | 05.05.21 | Logston   | Python and pandas               | 60FA 314  | 92    | Dümmlers Chapter 14 | e.logston@foo.edu |
| 4             | 2          | 04.07.21 | Nevarez   | Spreadsheet aggregate functions | WWH 201   | 65    | Zehnder Page 87     | i.nevarez@foo.edu |
| ...           | ...        | ...      | ...       | ...                             | ...       | ...   | ...                 | ...               |

In this data set, `assignment_id` and `student_id` form a composite primary key, with `assignment_id` representing a unique assignment and `student_id` representing a unique student. The table contains additional attributes such as `due_date`, `professor`, `assignment_topic`, `classroom`, `grade`, `relevant_reading`, and `professor_email`.

## 2. Normalization Issues

To comply with 4NF, the original data set must be 3NF-compliant and thus 2NF-compliant as well. The original data set fails to meet these requirements due to the following normalization issues:

### 2NF Violation

The dataset fails to meet 2NF because some attributes, such as `professor`, `classroom`, and `professor_email`, only depend on part of the composite primary key (`assignment_id`) and not on the full composite key (`assignment_id` and `student_id`). This partial dependency indicates that the table contains redundant information about professors and classrooms across records with the same `assignment_id`.

### 3NF Violation

A 3NF violation occurs with the `professor_email` attribute, which is transitively dependent on the `professor` attribute rather than directly on the composite primary key. This means `professor_email` can be determined by knowing the `professor` alone, leading to unnecessary redundancy and potential inconsistency if a professor's email changes.

### 4NF Violation

The table violates 4NF due to the presence of multi-valued dependencies. For example, an `assignment_id` could be linked to multiple professors, classrooms, and due dates for different sections of the same course. This indicates that attributes such as `professor` and `classroom` can independently take on multiple values for the same `assignment_id`, which is not permissible under 4NF without further normalization.

## 3. Tables in 4NF

1. Courses
   - `course_id` (Primary Key)
   - `course_name`
2. Professors
   - `professor_id` (Primary Key)
   - `professor_name`
   - `professor_email`
3. Sections
   - `section_id` (Primary Key)
   - `section_name`
   - `course_id` (Foreign Key)
   - `professor_id` (Foreign Key)
   - `classroom`
4. Assignments
   - `assignment_id` (Primary Key)
   - `assignment_topic`
   - `relevant_reading`
5. Assignment Instances
   - `assignment_instance_id` (Primary Key)
   - `assignment_id` (Foreign Key)
   - `section_id` (Foreign Key)
   - `due_date`
6. Students
   - `student_id` (Primary Key)
   - `student_name`
7. Grades
   - `assignment_instance_id` (Foreign Key)
   - `student_id` (Foreign Key)
   - `grade`
   - (Composite Primary Key: `assignment_instance_id`, `student_id`)

## 4. Entity-Relationship Diagrams

![ERD](./images/ERD.svg)

## 5. Changes Made to Meet 4NF

### Transition from 1NF to 2NF

- Identified composite primary keys where necessary, particularly `assignment_id` and `student_id` to uniquely identify each record.
- Addressed partial dependencies by separating data into different tables where each non-key attribute is fully functionally dependent on the primary key.

### Transition from 2NF to 3NF

Eliminate transitive dependencies to ensure every non-key attribute is directly dependent on the primary key.

- Introduced separate tables for Professors and Courses, removing transitive dependencies such as a professor's email being dependent on the professor rather than the primary key of the assignments.
- This separation ensured all non-key attributes in any table are directly dependent on that table’s primary key.

### Transition from 3NF to 4NF

Then addressed multi-valued dependencies by creating separate tables for multi-valued attributes, ensuring they are fully dependent on the primary key.

- Created an "Assignment Instances" table to accommodate scenarios where the same assignment could be given to different sections of the same course with varying due dates. This addressed the multi-valued dependencies by linking each assignment instance to a specific section, thereby making the assignment's attributes (like due date) dependent on the combined key of assignment and section.
- The "Assignments" table was refined to hold only the generic attributes of assignments, like the topic and relevant readings, independent of the sections they're assigned to.
- Adjusted the "Grades" table to reference "Assignment Instances" instead of "Assignments" directly, ensuring grades are tied to specific instances of assignments.

### Summary

By identifying and resolving partial, transitive, and multi-valued dependencies through the decomposition of the original table into seperated tables:

- Each table represents a single theme or entity type.
- All attributes in a table are fully functionally dependent on a "whole" primary key (2NF compliance).
- There are no transitive dependencies within tables (3NF compliance).
- Multi-valued dependencies are correctly managed by isolating them in their tables where they depend on the entire primary key (4NF compliance).
