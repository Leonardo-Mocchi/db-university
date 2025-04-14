# QUERIES PER LA CONSEGNA

## JOIN

1. Selezionare tutti gli studenti iscritti al Corso di Laurea in Economia

    > **SELECT** `students`.*,  
    `degrees`.`id`,  
    `degrees`.`name` **AS** `degree_name`  
    > **FROM** `students`  
    > **INNER JOIN** `degrees`  
    > **ON** `students`.`degree_id` = `degrees`.`id`  
    > **WHERE** `degrees`.`name` = "Corso di Laurea in Economia";  
    >
    >> 68 row(s) returned

2. Selezionare tutti i Corsi di Laurea Magistrale del Dipartimento di Neuroscienze

    > **SELECT** *  
    > **FROM** `degrees`  
    > **INNER JOIN** `departments`  
    > **ON** `degrees`.`department_id` = `departments`.`id`  
    > **WHERE** `degrees` . `level` = "magistrale" **AND** `departments` . `name` **LIKE** "%neuroscienze%";  
    >
    >> 1 row(s) returned  
    >> Corso di Laurea Magistrale in Odontoiatria e Protesi Dentaria

3. Selezionare tutti i corsi in cui insegna Fulvio Amato (id=44)

    > **SELECT** *  
    > **FROM** `courses`  
    > **INNER JOIN** `course_teacher`  
    > **ON** `courses` . `id` = `course_teacher` . `course_id`  
    > **WHERE** `course_teacher` . `teacher_id` = "44";  
    >
    >> 11 row(s) returned

4. Selezionare tutti gli studenti con i dati relativi al corso di laurea a cui sono iscritti e il relativo dipartimento,   in ordine alfabetico per cognome e nome

    > **SELECT** *  
    > **FROM** `students`  
    > **INNER JOIN** `degrees`  
    > **ON** `students` . `degree_id` = `degrees` . `id`  
    > **ORDER BY** `students` . `surname`,   `students` . `name` **ASC**;  

5. Selezionare tutti i corsi di laurea con i relativi corsi e insegnanti

    > **SELECT** `degrees` . `id` **AS** `degree_id`,  
    `degrees` . `name` **AS** `degree_name`,  
    `degrees` . `level` **AS** `degree_level`,  
    `degrees` . `address` **AS** `degree_address`,  
    `degrees` . `email` **AS** `degree_email`,  
    `degrees` . `website` **AS** `degree_website`,  
    `courses` . `id` **AS** `course_id`,  
    `courses` . `name` **AS** `course_name`,  
    `courses` . `description` **AS** `course_description`,  
    `courses` . `period` **AS** `course_period`,  
    `courses` . `year` **AS** `course_year`,  
    `courses` . `cfu` **AS** `course_cfu`,  
    `courses` . `website` **AS** `course_website`,  
    `course_teacher` . *,  
    `teachers` . `name` **AS** `teacher_name`,  
    `teachers` . `surname` **AS** `teacher_surname`,  
    `teachers` . `phone` **AS** `teacher_phone`,  
    `teachers` . `email` **AS** `teacher_email`,  
    `teachers` . `office_address` **AS** `teacher_office_address`,  
    `teachers` . `office_number` **AS** `teacher_office_number`  
    > **FROM** `degrees`  
    > **INNER JOIN** `courses`  
    > **ON** `degrees` . `id` = `courses` . `degree_id`  
    > **INNER JOIN** `course_teacher`  
    > **ON** `courses` . `id` = `course_teacher` . `course_id`  
    > **INNER JOIN** `teachers`  
    > **ON** `course_teacher` . `teacher_id` = `teachers` . `id`;  

6. Selezionare tutti i docenti che insegnano nel Dipartimento di Matematica (54)

    > **SELECT DISTINCT**  
    `teachers` . `id` **AS** `teachers_id`,  
    `teachers` . `name` **AS** `teachers_name`  
    `teachers` . `surname` **AS** `teachers_surname`  
    `departments` . `name` **AS** `department_name`  
    > **FROM** `teachers`  
    > **INNER JOIN** `course_teacher`  
    > **ON** `teachers` . `id` = `course_teacher` . `teacher_id`  
    > **INNER JOIN** `courses`  
    > **ON** `course_teacher` . `course_id` = `courses` . `id`  
    > **INNER JOIN** `degrees`  
    > **ON** `courses` . `degree_id` = `degrees` . `id`  
    > **INNER JOIN** `departments`  
    > **ON** `degrees` . `department_id` = `departments` . `id`  
    >
    >> 54 row(s) returned

7. BONUS: Selezionare per ogni studente il numero di tentativi sostenuti per ogni esame,   stampando anche il voto massimo. Successivamente, filtrare i tentativi con voto minimo 18.

    > **SELECT**  
    `students.id` **AS** `student_id`,  
    `students.name` **AS** `student_name`,  
    `students.surname` **AS** `student_surname`,  
    **COUNT**(`exam_student` . `exam_id`) **AS** `total_attempts`,  
    **MAX**(`exam_student` . `vote`) **AS** `max_vote`  
    > **FROM** `students`  
    > **INNER JOIN** `exam_student`  
    > **ON** `students.id` = `exam_student` . `student_id`  
    > **WHERE** `exam_student` . `vote` >= 18  
    > **GROUP BY** `students.id`  
    > **ORDER BY** `students.surname`,`students.name` **ASC**;

## GROUP BY

1. Contare quanti iscritti ci sono stati ogni anno

    > **SELECT** **SUBSTRING**(`students` . `enrolment_date`, 1, 4) **AS** `enrolment_year`,  
    > **COUNT**(`students` . `id`) **AS** `total_students`  
    > **FROM** `students`  
    > **GROUP BY** `enrolment_year`  
    > **ORDER BY** `enrolment_year` **ASC**;  
    >
    >> |enrolment_year|total_students|
    >> |:------------:|:------------:|
    >> |2018          |912           |
    >> |2019          |1709          |
    >> |2020          |1645          |
    >> |2021          |734           |

2. Contare gli insegnanti che hanno l'ufficio nello stesso edificio

   > **SELECT** `teachers` . `office_address`,  
   > **COUNT**(`teachers` . `office_address`) **AS** `teachers_per_building`  
   > **FROM** `teachers`  
   > **GROUP BY** `teachers` . `office_address`  
   > **ORDER BY** `teachers` . `office_address` **ASC**;  

3. Calcolare la media dei voti di ogni appello d'esame

   > **SELECT**  
    `exam_student` . `exam_id`,  
   > **COUNT**(`exam_student` . `exam_id`) **AS** `students_per_exam_session`,  
   > **ROUND**(**AVG**(`exam_student` . `vote`), 0) **AS** `avg_vote`  
   > **FROM** `exams`  
   > **INNER JOIN** `exam_student`  
   > **ON** `exams` . `id` = `exam_student` . `exam_id`  
   > **GROUP BY** `exam_student` . `exam_id`  
   > **ORDER BY** `exam_student` . `exam_id` **ASC**;

4. Contare quanti corsi di laurea ci sono per ogni dipartimento

   > **SELECT**  
    `departments` . `name`,  
    **COUNT**(`departments` . `id`) **AS** `majors_per_deprtment`  
   > **FROM** `degrees`  
   > **INNER JOIN** `departments`  
   > **ON** `degrees` . `department_id` = `departments` . `id`  
   > **GROUP BY** `departments` . `id`  
   > **ORDER BY** `departments` . `name` **ASC**;
