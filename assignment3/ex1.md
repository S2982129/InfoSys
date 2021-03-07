# Information Systems - Schema Definition and Simple Queries

Use this markdown file to submit your prepared SQL statements for each problem.

*Fill in your names and student numbers below:*

```
Lazaros Kogioumtzidis - s4109651
Thijs Visee - s2982129
```

## Problem 1 - Schema Definition
```
PRAGMA foreign_keys = ON; -- Foreign key constrains

CREATE TABLE `loot_item` (
`name` TEXT UNIQUE PRIMARY KEY,
`rarity` REAL NOT NULL,
CHECK (rarity > 0 AND rarity < 1)
);
 
CREATE TABLE `participant` (
`id` INTEGER UNIQUE PRIMARY KEY,
`type` TEXT NOT NULL, -- Check if this is right for TEXT;
'rank' INTEGER NOT NULL,
CHECK (type IS 'player' OR type IS 'party') 
);
-- Check if this is right for this;

CREATE TABLE `game` (
`id` INTERGER UNIQUE PRIMARY KEY,
`started_at` TEXT, -- Check if this is right for this
`ended_at` TEXT -- Check if this is right for this
); 

CREATE TABLE `game_participants` (
`game_id` INTEGER NOT NULL,
`participant_id` INTEGER NOT NULL,
PRIMARY KEY (`participant_id`,`game_id`),
FOREIGN KEY (game_id) REFERENCES game(id) ON UPDATE CASCADE ON DELETE CASCADE,
FOREIGN KEY (participant_id) REFERENCES participant(id) ON UPDATE CASCADE ON DELETE CASCADE
);

CREATE TABLE `player` (
`username` TEXT PRIMARY KEY, --Check here
`joined_at` DATETIME,
`xp_level` INTEGER NOT NULL,--Check here
`participant_id` INTEGER NULL,
FOREIGN KEY (participant_id) REFERENCES participant(id) ON UPDATE CASCADE ON DELETE CASCADE
);

CREATE TABLE `game_result` (
`game_id` INTEGER PRIMARY KEY,
`winner_id` INTEGER NOT NULL,
`score` INTEGER NOT NULL,
FOREIGN KEY (game_id) REFERENCES game(id) ON UPDATE CASCADE ON DELETE CASCADE,
FOREIGN KEY (winner_id) REFERENCES participant(id) ON UPDATE CASCADE ON DELETE CASCADE
);

CREATE TABLE `player_items` (
`player_username` TEXT,
`item_name` TEXT,
`count` INTEGER NOT NULL
PRIMARY KEY (`player_username`,`item_name`),
FOREIGN KEY (player_username) REFERENCES player(username) ON UPDATE CASCADE ON DELETE CASCADE,
FOREIGN KEY (item_name) REFERENCES loot_item(name) ON UPDATE CASCADE ON DELETE CASCADE
);

CREATE TABLE `party` (
`id` INTEGER NOT NULL,
`created_by` TEXT,
`participant_id` INTEGER NULL,
PRIMARY KEY (`id`)
FOREIGN KEY (created_by) REFERENCES player(username) ON UPDATE CASCADE ON DELETE CASCADE,
FOREIGN KEY (participant_id) REFERENCES participant(id) ON UPDATE CASCADE ON DELETE CASCADE
);



CREATE TABLE `party_players` (
`party_id` INTEGER,
`player_username` TEXT,
`jointed_at` DATETIME,
`left_at` DATETIME,
PRIMARY KEY (`party_id`,`player_username`),
FOREIGN KEY (party_id) REFERENCES party(id) ON UPDATE CASCADE ON DELETE CASCADE,
FOREIGN KEY (player_username) REFERENCES player(username) ON UPDATE CASCADE ON DELETE CASCADE
);


CREATE TABLE `Report` (
`id` INTEGER PRIMARY KEY,
`sent_by` TEXT,
`sent_at` DATETIME,
`report_text` TEXT,
`is_read` TEXT,
`reported_player` TEXT,
FOREIGN KEY (sent_by) REFERENCES player(username) ON UPDATE CASCADE ON DELETE CASCADE,
FOREIGN KEY (reported_player) REFERENCES player(username) ON UPDATE CASCADE ON DELETE CASCADE
);



insert into game_participants (game_id,participant_id) VALUES ('60',43');

insert into game (id, started_at, ended_at) VALUES ('1','today','yesterday');
 insert into participant (id, type, rank) VALUES ('16','player','30');
```

## Problem 2 - Assorted Queries

```

-- 2.1
-- Select all users
SELECT *
FROM `users`
ORDER BY `users`.`created_at` DESC; 

-- Select only the newest user
SELECT`id`, `name`, `email`, MAX(`created_at`) FROM `users`;

-- 2.2 TODO: enter your email
INSERT INTO `users` (`name`, `email`) VALUES 
('Thijs Visee', 't.p.visee@student.rug.nl'),
('Lazaros Kogioumtzidis', 'email');

-- 2.3
-- Add a workout
INSERT INTO `workouts` (`user_id`)
SELECT `users`.`id`
FROM `users`
WHERE `users`.`name` = 'Thijs Visee';

-- Add at least 2 exercises to the workout
INSERT INTO `exercise_metrics` (`duration`, `repetitions`, `weight`) VALUES
(NULL, 10, 2);
INSERT INTO `workout_exercises` (`workout_id`, `exercise_name`, `exercise_metric_id`) VALUES
(
	(SELECT MAX(`id`) FROM `workouts`),
	'Deadlift',
	(SELECT MAX(`id`) FROM `exercise_metrics`)
);

INSERT INTO `exercise_metrics` (`duration`, `repetitions`, `weight`) VALUES
(NULL, 24, NULL);
INSERT INTO `workout_exercises` (`workout_id`, `exercise_name`, `exercise_metric_id`) VALUES
(
	(SELECT MAX(`id`) FROM `workouts`),
	'Squat',
	(SELECT MAX(`id`) FROM `exercise_metrics`)
);

INSERT INTO `exercise_metrics` (`duration`, `repetitions`, `weight`) VALUES
(NULL, 24, NULL);
INSERT INTO `workout_exercises` (`workout_id`, `exercise_name`, `exercise_metric_id`) VALUES
(
	(SELECT MAX(`id`) FROM `workouts`),
	'Pull Up',
	(SELECT MAX(`id`) FROM `exercise_metrics`)
);

-- Also for Partner
INSERT INTO `workouts` (`user_id`)
SELECT `users`.`id`
FROM `users`
WHERE `users`.`name` = 'Lazaros Kogioumtzidis';

-- Add at least 2 exercises once again
INSERT INTO `exercise_metrics` (`duration`, `repetitions`, `weight`) VALUES
(NULL, 50, 60);
INSERT INTO `workout_exercises` (`workout_id`, `exercise_name`, `exercise_metric_id`) VALUES
(
	(SELECT MAX(`id`) FROM `workouts`),
	'Bench Press',
	(SELECT MAX(`id`) FROM `exercise_metrics`)
);

INSERT INTO `exercise_metrics` (`duration`, `repetitions`, `weight`) VALUES
(NULL, 24, 80);
INSERT INTO `workout_exercises` (`workout_id`, `exercise_name`, `exercise_metric_id`) VALUES
(
	(SELECT MAX(`id`) FROM `workouts`),
	'Overhead Press',
	(SELECT MAX(`id`) FROM `exercise_metrics`)
);

INSERT INTO `exercise_metrics` (`duration`, `repetitions`, `weight`) VALUES
(30, NULL, NULL);
INSERT INTO `workout_exercises` (`workout_id`, `exercise_name`, `exercise_metric_id`) VALUES
(
	(SELECT MAX(`id`) FROM `workouts`),
	'Running',
	(SELECT MAX(`id`) FROM `exercise_metrics`)
);

-- 2.4
SELECT `name`, `email`
FROM `users`
WHERE `users`.`email` LIKE '%.com'
ORDER BY `users`.`email` ASC;


-- 2.5
-- Rename "Bench Press" exercise to "Barbell Bench Press"
UPDATE `exercises`
SET `name` = 'Barbell Bench Press'
WHERE `name` LIKE 'Bench Press';

-- Delete user JOE MAMMA
DELETE FROM `users`
WHERE `users`.`name` = 'Joe Mamma';

```

