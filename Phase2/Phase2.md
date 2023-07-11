
```sql
USE Team12LMS;

CREATE TABLE departments (
  subject VARCHAR(4) NOT NULL PRIMARY KEY,
  name VARCHAR(100) NOT NULL
);

CREATE TABLE students (
  uid CHAR(8) PRIMARY KEY NOT NULL,
  first_name VARCHAR(100) NOT NULL,
  last_name VARCHAR(100) NOT NULL,
  date_of_birth DATE NOT NULL,
  department VARCHAR(4) NOT NULL,
  CONSTRAINT FK_department_student FOREIGN KEY (department) REFERENCES departments(subject) ON UPDATE CASCADE ON DELETE CASCADE
);

CREATE TABLE professors (
  uid CHAR(8) PRIMARY KEY NOT NULL,
  first_name VARCHAR(100) NOT NULL,
  last_name VARCHAR(100) NOT NULL,
  date_of_birth DATE NOT NULL,
  department VARCHAR(4) NOT NULL,
  CONSTRAINT FK_department_professor FOREIGN KEY (department) REFERENCES departments(subject) ON UPDATE CASCADE ON DELETE CASCADE
);

CREATE TABLE administrators (
  uid CHAR(8) PRIMARY KEY NOT NULL,
  first_name VARCHAR(100) NOT NULL,
  last_name VARCHAR(100) NOT NULL,
  date_of_birth DATE NOT NULL
);

CREATE TABLE courses (
  id INT UNSIGNED NOT NULL AUTO_INCREMENT PRIMARY KEY,
  department VARCHAR(4) NOT NULL,
  number SMALLINT UNSIGNED NOT NULL,
  name VARCHAR(100) NOT NULL,
  CONSTRAINT unique_department_number UNIQUE (department, number),
  CONSTRAINT FK_department_courses FOREIGN KEY (department) REFERENCES departments(subject) ON UPDATE CASCADE ON DELETE CASCADE
);

CREATE TABLE classes (
  id INT UNSIGNED NOT NULL AUTO_INCREMENT PRIMARY KEY,
  course_id INT UNSIGNED NOT NULL,
  professor_id CHAR(8) NOT NULL,
  year SMALLINT UNSIGNED NOT NULL,
  season ENUM('Spring', 'Fall', 'Summer') NOT NULL,
  start_time TIME NOT NULL,
  end_time TIME NOT NULL,
  location VARCHAR(100) NOT NULL,
  CONSTRAINT unique_course_year_season UNIQUE (course_id, year, season),
  CONSTRAINT FK_course_id FOREIGN KEY (course_id) REFERENCES courses(id) ON UPDATE CASCADE ON DELETE CASCADE,
  CONSTRAINT FK_professor_id FOREIGN KEY (professor_id) REFERENCES professors(uid) ON UPDATE CASCADE ON DELETE CASCADE
);

CREATE TABLE enrollment (
  student_id CHAR(8) NOT NULL,
  class_id INT UNSIGNED NOT NULL,
  grade ENUM('A', 'A-', 'B+', 'B', 'B-', 'C+', 'C', 'C-', 'D+', 'D', 'D-', 'E', 'X', 'WF', 'EW', 'EU', 'F'),
  PRIMARY KEY (student_id, class_id),
  CONSTRAINT FK_student_id FOREIGN KEY (student_id) REFERENCES students(uid) ON UPDATE CASCADE ON DELETE CASCADE,
  CONSTRAINT FK_class_id FOREIGN KEY (class_id) REFERENCES classes(id) ON UPDATE CASCADE ON DELETE CASCADE
);

CREATE TABLE assignment_categories (
  id INT UNSIGNED NOT NULL AUTO_INCREMENT PRIMARY KEY,
  class_id INT UNSIGNED NOT NULL,
  name VARCHAR(100) NOT NULL,
  weight SMALLINT UNSIGNED NOT NULL,
  CONSTRAINT unique_name_class_id UNIQUE (name, class_id),
  CONSTRAINT check_weight_range CHECK (weight >= 0 AND weight <= 100),
  CONSTRAINT FK_class_id_2 FOREIGN KEY (class_id) REFERENCES classes(id) ON UPDATE CASCADE ON DELETE CASCADE
);

CREATE TABLE assignments (
  id INT UNSIGNED NOT NULL AUTO_INCREMENT PRIMARY KEY,
  category_id INT UNSIGNED NOT NULL,
  name VARCHAR(100) NOT NULL,
  contents VARCHAR(8192) NOT NULL,
  points SMALLINT UNSIGNED NOT NULL,
  due DATETIME NOT NULL,
  CONSTRAINT unique_assignment_name UNIQUE (name),
  CONSTRAINT FK_category_id FOREIGN KEY (category_id) REFERENCES assignment_categories(id) ON UPDATE CASCADE ON DELETE CASCADE
);

CREATE TABLE submission (
  student_id CHAR(8) NOT NULL,
  assignment_id INT UNSIGNED NOT NULL,
  time DATETIME NOT NULL,
  contents VARCHAR(8192) NOT NULL,
  score SMALLINT UNSIGNED,
  PRIMARY KEY (student_id, assignment_id),
  CONSTRAINT FK_student_id_2 FOREIGN KEY (student_id) REFERENCES students(uid) ON UPDATE CASCADE ON DELETE CASCADE,
  CONSTRAINT FK_assignment_id_2 FOREIGN KEY (assignment_id) REFERENCES assignments(id) ON UPDATE CASCADE ON DELETE CASCADE
);

DELIMITER $$
CREATE TRIGGER submission_score_check_insert
BEFORE INSERT ON submission
FOR EACH ROW
BEGIN
  DECLARE assignment_points SMALLINT UNSIGNED;
  SELECT points INTO assignment_points FROM assignments WHERE id = NEW.assignment_id;
  IF NEW.score IS NOT NULL AND NEW.score > assignment_points THEN
    SIGNAL SQLSTATE '45000' SET MESSAGE_TEXT = 'Score cannot exceed assignment points.';
  END IF;
END$$

CREATE TRIGGER submission_score_check_update
BEFORE UPDATE ON submission
FOR EACH ROW
BEGIN
  DECLARE assignment_points SMALLINT UNSIGNED;
  SELECT points INTO assignment_points FROM assignments WHERE id = NEW.assignment_id;
  IF NEW.score IS NOT NULL AND NEW.score > assignment_points THEN
    SIGNAL SQLSTATE '45000' SET MESSAGE_TEXT = 'Score cannot exceed assignment points.';
  END IF;
END$$
DELIMITER ;

```