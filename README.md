# FlyWire Challenge – Biggest Shared Circuit

## What I found

I found a group of **1,188 neurons** that have the exact same wiring shape in
three of the five datasets: **MCNS, FAFB, and MAOL**. The shape is a "star" – one
central neuron (the hub) that connects out to 1,187 other neurons, and those 1,187
don't connect to each other. So it's 1,188 neurons and 1,187 connections, and it
looks identical in all three datasets (same connections, arrows pointing the same
way).

I matched the neurons only by their wiring, not by what type of cell they are. The
challenge said that's allowed, and the datasets come from different flies and brain
areas anyway, so the matched neurons aren't the same cells – they just happen to
have the same connection pattern.

## Files in here

- `network.csv` – the matched neurons. 3 columns (MCNS, FAFB, MAOL), 1,188 rows.
  The first row is the hub in each dataset, the rest are the leaves.
- `science.md` – my short write-up about the biology.
- `flywire_challenge.py` – the main code. It loads the datasets, finds the circuit,
  checks it's correct, and saves the CSV.
- `draw_clean_circuit.py` – makes the picture of the circuit.
- `figures/` – the circuit pictures.

## How I thought about it

The task: pick 3 of the 5 datasets, find a group of neurons in each one, and the
connections between those neurons have to be identical across all 3 (same
connections, same arrow directions). Make the group as big as possible, and it has
to be "weakly connected" – meaning it's all one connected piece, not separate
clumps.

One thing I noticed early: if you just pick a bunch of neurons that don't connect
to each other at all, that technically counts as "identical" (nothing matches
nothing), and you could get a giant number that way. But that's not really a
circuit, and the weakly-connected rule blocks it. So I looked for a real connected
shape instead.

I went with a **star** shape for a few reasons:
- Any two stars with the same number of leaves automatically have the same shape,
  so I don't have to worry about whether they'll match – they just do.
- A star is always connected, because every leaf touches the hub.
- It's easy to find a big one: take a neuron with lots of connections, then keep
  the ones that don't connect to each other.
- It actually means something – one neuron sending signals out to a lot of others.

How the code finds it:
1. Load each dataset as a directed graph (arrows = connections; I ignored the
   synapse counts like the instructions said).
2. For each dataset, look at the most-connected neurons and build the biggest clean
   star around each one. I check both directions (hub sending out, hub receiving
   in).
3. Take the 3 datasets with the biggest stars and cut them down to the same size so
   they all match.

To be honest about it: this isn't guaranteed to be the absolute biggest possible
circuit – that's basically impossible to prove on graphs this huge. It's a big one
that I can prove is correct.

## How to run it

```
pip install pandas networkx matplotlib
```
Put the 5 edge-list CSVs in the same folder, then:
```
python flywire_challenge.py     # makes solution.csv (I renamed it to network.csv)
python draw_clean_circuit.py    # makes the figure
```

## The numbers

Biggest star I found in each dataset:

| Dataset | Best "out" star | Best "in" star |
|---------|----------------:|---------------:|
| MCNS | **2,118** | 1,688 |
| FAFB | **2,062** | 1,561 |
| MAOL | **1,188** | 890 |
| BANC | 783 | 1,096 |
| MANC | 248 | 257 |

The three biggest "out" stars were MCNS, FAFB, and MAOL, so I used those three and
cut them all down to MAOL's size, which is where 1,188 comes from.

## Checking it's right

The code double-checks the answer on the real data: each one has exactly 1,187
connections, none of the leaves connect to each other, the whole thing is one
connected piece, and all three are confirmed to be the same shape with the arrows
pointing the same way. If anything was wrong it would stop with an error – it
didn't, so the answer is valid.

## Limitations

It's a shortcut (a heuristic), not a proven maximum. A star is a simple shape;
fancier shapes might be more interesting but are much harder to find and match.
Also, because these brains are pretty spread out (not many of any given group of
neurons connect to each other), the "leaves don't connect" part is easy to get, so
the size mostly comes from how many connections the hub neuron has.
