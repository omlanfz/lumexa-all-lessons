# Proposed Content Schema (Non-binding — for future Lumexa dashboard integration)

This is a lightweight, technology-neutral proposal for how these lessons could eventually be
represented in Lumexa's student/teacher dashboards. It does not reflect any confirmed backend
architecture.

- **Course:** id, title, age_range, tools[], final_deliverables[]
- **Lesson:** id, course_id, order, title, duration_min, prerequisites[], objectives[],
  deliverables[], skills[], resource_refs[] (instructor guide, student lesson, worksheet,
  project), rubric_ref
- **Project:** id, name, type (coding | non-coding), folder_path, readme_ref
- **Submission:** student_id, lesson_id, submitted_artifact_ref, status, reviewed_by, feedback

See `Lesson_Metadata_Template.json` and `Project_Metadata_Template.json` for example records.
