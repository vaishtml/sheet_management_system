### Sheet Management System

This n8n workflow automates the process of reading an unstructured class schedule and distributing personalized timetables to individual mentors.

## Problem Statement
This project is based on real-life problem.
In our college, Mentors all use the same fixed sheet with the full schedule.
The sheet includes date, hall number, class name, and mentor assigned.
Mentors have to constantly check this sheet.
They create separate personal sheets manually, which is repetitive and time-consuming.

I have created a google form and shared it with my mentors, where they addressed their concerns. Using this data, I have narrowed it down to the most common problem they face.

To import the template into your n8n, you can import the json file in the github and see the live workflow.

***

### Overview
The workflow runs daily at **7:30 AM** (or manually) and performs two main tasks:
1.  **Data Structuring**: It pulls raw class data and a list of mentors from two different Google Sheets, combines them, and processes the messy schedule data into a clean, structured format, which is then saved to a Supabase database.
2.  **Personalized Distribution**: It filters the structured data to create a separate, personalized schedule for each mentor (John, Alice, Nick, etc.) and appends that information to their respective Google Sheet.

***

### Workflow Steps

#### 1. Data Ingestion & Structuring
* **Schedule Trigger / When clicking 'Execute workflow'**: Starts the workflow daily at 7:30 AM or on manual execution.
* **Demo Schedule** and **Mentors DB**: Read data from two Google Sheets: the raw class timetable and a list of mentor names.
* **Merge**: Combines the data from the two Google Sheets into a single set of items.
* **Structure the Data**: A custom code node that performs the crucial step of turning the raw data into a structured format. It:
    * Maps column names to specific time slots (e.g., `col_5` becomes `8:30-9:30`).
    * Separates mentor names from class names within the schedule cells.
    * Creates structured records with fields like `day`, `date`, `section`, `time_slot`, `class_name`, and `mentor_name`.
* **Structured Table (Supabase)**: Saves the clean, structured class data into a Supabase database table named `timetable`.

#### 2. Personalized Mentor Schedule Distribution
* **Individual Mentor Details**: A custom code node that groups the structured data by `mentor_name`, creating a single item for each mentor that contains a list of all their assigned classes.
* **Flatten the Data**: A custom code node that flattens the grouped data back into individual class records, but now each record has a confirmed mentor name.
* **Switch**: This node checks the `mentor_name` field for each class record and routes the data to the correct personalized schedule node.
    * It checks for mentors named "John," "Alice," and "Nick."
* **John, Alice, Nick (Google Sheets)**: These nodes append the respective mentor's class data to their personalized Google Sheet, providing each mentor with their individual schedule.
