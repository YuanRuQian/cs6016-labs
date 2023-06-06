# Lab 3: Relational Algebra

## Part 1 - Joins

### $T1 \bowtie _{T1.A = T2.A} T2$

| A   | Q | R | B | C |
| --- | - | - | - | - |
| 20  | a | 5 | b | 6 |
| 20  | a | 5 | b | 5 |

### $T1 \bowtie _{T1.Q = T2.B} T2$

Empty set (no results)

### $T1 \bowtie  T2$

| A   | Q | R | B | C |
| --- | - | - | - | - |
| 20  | a | 5 | b | 6 |
| 20  | a | 5 | b | 5 |

###  $T1 \bowtie _{T1.A = T2.A \wedge T1.R = T2.C} T2$

| A   | Q | R | B | C |
| --- | - | - | - | - |
| 20  | a | 5 | b | 5 |

## Part 2 - Chess Queries

### Find the names of any player with an Elo rating of 2850 or higher.

$\Pi_{\text{Name}}(\sigma_{\text{Elo} \geq 2850}(\text{Players}))$

### Find the names of any player who has ever played a game as white.

$\rho({\text{WhitePlayerID}},(\Pi_\text{wpID}(\text{Games})))$

$\Pi_{\text{Name}}(\text{Players} \bowtie _\text{Players.pID = WhitePlayerID.wpID} (\text{WhitePlayerID}))$


### Find the names of any player who has ever won a game as white.

$\Pi_{\text{Name}}(\text{Players} \bowtie_{{\text{(Players.pID = Games.wpID)} \land {\text{(Games.Result = '1-0')}}}}(\text{Games}))$

### Find the names of any player who played any games in 2018.

$\Pi_{\text{Name}}(\text{Players} \bowtie_{(({\text{Players.pID = wpID}}) \lor ({\text{Players.pID = bpID}})) \land ({\text{Games.Year = 2018}})}(\text{Games}))$

### Find the names and dates of any event in which Magnus Carlsen lost a game.

$\rho({\text{MCPlayerID}},(\Pi_\text{pID}(\sigma_{\text{Name} = \text{'Magnus Carlsen'}}(\text{Players}))))$

$\rho({\text{MCLostAsBP},\Pi_\text{(Event.Name, Event.Year)}}((\text{Games} \bowtie_{(\text{Games.Result = '1-0'} \land \text{Games.bpID = MCPlayerID.pID})} \text{MCPlayerID}) \bowtie_{\text{Event.eID = Games.eID}} (\text{Event})))$

$\rho({\text{MCLostAsWP},\Pi_\text{(Event.Name, Event.Year)}}((\text{Games} \bowtie_{(\text{Games.Result = '0-1'} \land \text{Games.wpID = MCPlayerID.pID})} \text{MCPlayerID}) \bowtie_{\text{Event.eID = Games.eID}} (\text{Event})))$

$\text{MCLostAsBP} \cup \text{MCLostAsWP}$

### Find the names of all opponents of Magnus Carlsen. An opponent is someone who he has played a game against.

$\rho({\text{MCPlayerID}},(\Pi_\text{pID}(\sigma_{\text{Name} = \text{'Magnus Carlsen'}}(\text{Players}))))$

$\rho({\text{MCAsBP}},(\text{Games} \bowtie_{ \text{Games.bpID = MCPlayerID.pID}} \text{MCPlayerID}))$

$\rho({\text{MCAsWP}},(\text{Games} \bowtie_{ \text{Games.wpID = MCPlayerID.pID}} \text{MCPlayerID}))$

$\Pi_{\text{Name}}((\text{Players} \bowtie _{\text{Players.pID = MCAsBP.wpID}} \text{MCAsBP}) \cup (\text{Players} \bowtie _{\text{Players.pID = MCAsWP.bpID}} \text{MCAsWP}))$

## Part 3 - LMS Queries

### Part 3.1

Table: 

| Name ( varchar(255) )    |
|----------|
| Hermione |
| Harry    |


To find the names of students who never had a "C" grade.

### Part 3.2

| Name ( varchar(255) )     |
|----------|
| Hermione |

To find the names of all students who were born in the same year as Ron, excluding Ron himself.

### Part 3.3

Empty set (no results)


To find the names of the courses in which every student is enrolled.

### Part 4



$\rho(\text{CoursesStartWithThree}, \left( \Pi_\text{cID}(\sigma_{(\text{cID} \geq 3000) \land (\text{cID} < 4000)} (\text{Courses}) \right)))$


$\rho(\text{StudentsIDs}, \left( \Pi_\text{sID} (\text{Enrolled} / \text{CoursesStartWithThree}) \right))$

$\Pi_{\text{{Name}}} \left( \text{{Students}} \bowtie_{\text{{Students.sID}} = \text{{StudentsIDs.sID}}} \text{{(StudentsIDs)}} \right)$
