# Lab 3: Relational Algebra

## Part 1 - Joins

### $T1 \bowtie _{T1.A = T2.A} T2$

| A   | Q | R | B | C |
| --- | - | - | - | - |
| 20  | a | 5 | b | 6 |
| 20  | a | 5 | b | 5 |

### $T1 \bowtie _{T1.Q = T2.B} T2$


Empty table

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

### Find the names of any player with an Elo rating of 2850 or higher

$\Pi _{Name}(\sigma _{Elo >= 2850} (Players))$

### Find the names of any player who has ever played a game as white

$\Pi _{Name}(Players \bowtie _{ (wpID = pID)} Games)$ 

### Find the names of any player who has ever won a game as white

$\Pi _{Name}(Players \bowtie _{((wpID = pID) \space and \space(Result = '1-0'))} Games)$

### Find the names of any player who played any games in 2018

$\Pi _{Name}(Players \bowtie _{(pID = wpID \space or \space pID = bpID)} (Games \bowtie _{(eID = eID \space and \space Year = 2018)} Events))$

### Find the names and dates of any event in which Magnus Carlsen lost a game

$\Pi _{Name, Year}(Events \bowtie _{(eID = eID \space and \space Name = 'Magnus Carlsen')} (Games \bowtie _{((wpID = pID \space and \space Result = '0-1') \space or \space (bpID = pID \space and \space Result = '1-0'))} Players))$

### Find the names of all opponents of Magnus Carlsen.

$\Pi _{Name}(
    \sigma _{pID} \space IN \space (
        \Pi wpID(
            \rho wpID/Games(
                σ bpID = (\Pi pID(σ Name = 'Magnus Carlsen'(Players)))
                Games
            )
        )
        ∪
        \Pi bpID(
            \rho bpID/Games(
                σ wpID = (\Pi pID(σ Name = 'Magnus Carlsen'(Players)))
                Games
            )
        )
    )
    Players
)
$