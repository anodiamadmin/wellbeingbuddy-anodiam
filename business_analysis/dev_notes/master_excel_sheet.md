# Master Excel Sheet

## 1. Class Details (The Infrastructure)

- **year_level** (Required)
- **class** (Required)

## 2. Teacher Details

- **teacher_honorifics** (Required)
- **teacher_first_name** (Required)
- **teacher_middle_name** (Optional - Leave blank if N/A)
- **teacher_last_name** (Required)
- **teacher_email** (Required - Crucial for backend deduplication)

## 3. Student Details

- **student_first_name** (Required)
- **student_middle_name** (Optional - Leave blank if N/A)
- **student_last_name** (Required)
- **student_dob** (Required - Format: DD-MM-YYYY)
- **student_gender** (Required - Must be one of the following DB Enum values):
  - `MALE`
  - `FEMALE`
  - `NON_BINARY`
  - `PREFER_NOT_TO_SAY`

  > A dropdown selector enforcing these values is present in the Excel sheet. Any other value will be rejected by the backend.

- **student_pronouns** (Optional - Leave blank if N/A)
  - Accepted examples: `She/Her`, `He/Him`, `They/Them`, `She/They`, `He/They`, `Ask Me`

  > **Note:** `Ask Me` is the safe default — it signals to the teacher that they should ask the student directly for their preferred pronouns.

## 4. Parent Details

- **parent_first_name** (Required)
- **parent_middle_name** (Optional - Leave blank if N/A)
- **parent_last_name** (Required)
- **parent_email** (Required - Crucial for backend deduplication)

---

## Edge Case Rule for Developers

If an Admin wants to onboard a Teacher and create a Class but there are no students enrolled yet, the Admin fills out **Sections 1 and 2 only**, leaving Sections 3 and 4 completely blank.

The FastAPI Pandas script will read `student_first_name` as `None` and safely skip student/parent creation for that row, while keeping the teacher and class records intact.