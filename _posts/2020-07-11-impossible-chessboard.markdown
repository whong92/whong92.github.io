---
layout: post_math
title:  "The impossible chessboard puzzle made possible"
date:   2020-07-11 22:19:00 +0100
author_profile: true
toc: true
classes: wide
categories: math 
---

One of my favorite creators on youtube on the channel [3Blue1Brown](https://www.youtube.com/channel/UCYO_jab_esuFRV4b17AJtAw) posted a video on the [Impossible Chessboard Puzzle](https://www.youtube.com/watch?v=wTJI_WuZSwE), (a link is included below). Here I will briefly try to explain what the original game is all about, and how its unsolvable for most cases. I also present an alternative game (with a very tiny change) that is (probably) solvable for all $$N$$. A [program I wrote](https://gist.github.com/whong92/c99088fbb5928b8af52b1d85514bebd1) also solves the game using a recursive backtracking search strategy.

## The Original Game

The rules of the game boiled down to its essence are:

- You are part of a 2-person team.
- You have a N-by-N chessboard in front of you, with a key hidden under one of the squares.
- The chessboard has $$K^2$$ coins on it, randomly set to an initial state of heads and tails (note randomly means ANY initial state of $$K^2$$ heads-tails combo is possible).
- You know the location of the key.
- You must communicate to your partner by flipping exactly ONE coin over, the location of the key.

If this is unclear, the video provides a much more detailed explanation of the rules of the game, and how to go about solving it.

<iframe width="560" height="315" src="https://www.youtube.com/embed/wTJI_WuZSwE" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## Solving the Original Game

In his video, Grant from [3Blue1Brown](https://www.youtube.com/channel/UCYO_jab_esuFRV4b17AJtAw) showed that it was impossible to solve the puzzle in its original form *unless* $$K^2$$ is a power of 2. Also it actually doesn't matter what the dimensions of the chessboard is (square or rectangular), as the number of squares is the only relevant piece of information, so instead of using $$K^2$$ to refer to the number of squares, we'll just use $$N=K^2$$ to refer to the number of squares.

To do this, it is shown that the problem can be translated into a $$2^N$$ dimensional hypercube, with each vertex of the hypercube representing a particular configuration of the chessboard. The combination of $$N$$ heads and tails can be thought of as a $$N$$-bit binary integer, which forms a vertex of a volume $$1\text{m}^N$$ $$N$$-dimensional hypercube. This hypercube has $$2^N$$ vertices, corresponding to the $$2^N$$ possibilities of the heads-tails configuration of $$N$$ coins.

Now we basically need to map each and every key position onto each vertex, such that starting from any vertex, it is possible to reach any other vertex representing any key position by traversing an edge (aka flipping a coin). Then the problem comes down to coloring the $$2^N$$ vertices into $$N$$ colors (one color per key position), such that from each vertex, it is possible to reach a vertex of ANY color.

See below shamelessly taken screenshots from the video for illustrations on $$N=3$$ (on a regular cube).

![](https://i.imgur.com/QcDfK04.png)
*Mapping chessboard configurations to vertices*

![](https://i.imgur.com/snirlNK.png)
*Arbitrary coloring the vertices - may or may not solve the puzzle*

What we then want, is shown in the image below, basically, for every vertex to have a neighbor (via its edges) to vertices of all different colors. This is basically a required property of the graph and we shall name this property $$P_0$$:

$$P_0$$: In a $$2^N$$ vertex hypercube graph, every vertex has at least one neighbour of every color $$i\in[0,N-1]$$ not including itself.


![](https://i.imgur.com/4WDB6ob.png)
*Coloring the vertices so that every vertex has access to every color*

Now for the twist. For the 3-coin / chessboard squares problem, this is unsatisfiable. To see this, let $$V_i$$ denote the number of vertices for color $$i$$, $$i\in[0,N-1]$$ (instead of $$N$$ colors its more convenient to think of $$N$$ integers instead). notice that we require $$2^N$$ incoming edges into every color, and for every $$i$$-th colored vertex, we have 3 incoming edges. We therefore require:

$$
\begin{equation}\label{eq:vertex_count_constraint}
N V_i \ge 2^N \quad \text{or} \quad V_i \ge \frac{2^N}{N}
\end{equation}
$$

For integer-valued $$V_i$$, this also means:

$$
\begin{align*}
V_i & \ge \left \lceil \frac{2^N}{N} \right \rceil \\
\sum_{i=0}^{N-1} V_i & \ge N \left \lceil \frac{2^N}{N} \right \rceil
\end{align*}
$$

For $$N$$ not a power of two, this would imply that $$\sum_i V_i$$ be greater than the total number of available vertices $$2^N$$, clearly an impossibility! 

So for this version of the game, if $$N$$ not a power of 2, the game is impossible.

If I explained that poorly, again, I'm sorry, but there's only so much I can write in a 4 paragraphs. Please have a look at the amazingly lucid explanation given in the original video.

## An Alternative Game

Now, if we alter the game *ever* so slightly, by changing one of the rules: instead of being forced to always flip *exactly* one, we can now choose to flip *at most* one coin. While this change might seem limiting (how does not flipping a coin help?), it actually makes the puzzle not impossible for $$N$$ not equal to a power of 2. As for whether its *always* possible, I honestly have no idea.

To see this, notice that the possibility of not flipping a coin, augments the hypercube, to have $$N$$ extra self-looping edges on each vertex, shown in the image below:

![](https://i.imgur.com/rqE4ggu.png)
*Adding self-loops to the original graph*

Another way of looking at this, is that, we have modified the required graph property from that of $$P_0$$, to the following:

$$P_1$$: In a $$2^N$$ vertex hypercube graph, every vertex has at least one neighbour of every color $$i\in[0,N-1]$$ *including* itself.

This means we can always stay at the current vertex, if we so choose, by looping back onto our starting point. This increases the number of incoming edges into each vertex from $$N$$ to $$N+1$$. This changes the vertex count constraint equation ($$\ref{eq:vertex_count_constraint}$$) to the following:

$$
\begin{equation} V_i \ge \frac{2^N}{N+1} \end{equation}
$$

This suddenly makes it possible to fit all our required colors into our hypercube, without violating the vertex constraint, since $$\sum_i V_i$$ is no longer guaranteed to be larger than $$2^N$$ for integer-valued $$V_i$$:

$$
\begin{align}
\sum_{i=0}^{N-1} V_i & \ge N \left \lceil \frac{2^N}{N+1} \right \rceil \\ 
 &\le 2^N
\end{align}
$$

The above inequality uses the fact that $$\frac{2^N}{N} \ge \left \lceil \frac{2^N}{N+1} \right \rceil $$, which holds for all integers $$N>1$$. Proof included in appendix.

We can also find an upper bound for each $$V_i$$, by noting that, given a valid allocation of $$V_i$$ that fills up the graph:

$$
\sum_{j=0}^{N-1} V_j =  2^N
$$

And, for vertices of the color $$i$$: 

$$
\begin{align*}
V_i & =  2^N - \sum_{j \neq i}^N V_j \\
V_i & \leq 2^N - (N-1) \frac{2^N}{N+1} \\
V_i & \leq \frac{2^{N+1}}{N+1} \\
\end{align*}
$$

Taken together, this means that we have, for each $$V_i$$:

$$
\begin{equation} \label{eq:vertex_constraint_2} \frac{2^N}{N+1} \leq V_i \leq \frac{2^{N+1}}{N+1} \end{equation}
$$

## Example Solutions for N=3 and N=4

Here I will show some example assignments that can solve the alternative game.

Using a simple recursive-backtracking solver, I was able to find some example solutions (aka valid color assignments) for the cases $$N=3$$ and $$N=4$$. I have included a link to the github-gist for the solver [here](https://gist.github.com/whong92/c99088fbb5928b8af52b1d85514bebd1). One modification I made to a naive backtracking solver, was to use the constraint on $$V_i$$ (equation ($$\ref{eq:vertex_constraint_2}$$)) to guide the backtracking search (I think it made finding the solution a little bit quicker).Here are some color assignments. Unfortunately, I ran out of steam writing this article and gave up on making visualizations.

Take the time to convince yourselves that for each vertex, all neighbors, including each vertex itself, contain all colors $$[0,1,2,...,N-1]$$

<table>
  {% for row in site.data.impossible-chessboard-N-3 %}
    {% if forloop.first %}
    <tr>
      {% for pair in row %}
        <th>{{ pair[0] }}</th>
      {% endfor %}
    </tr>
    {% endif %}

    {% tablerow pair in row %}
      {{ pair[1] }}
    {% endtablerow %}
  {% endfor %}
</table>
*Example solution for a $$N=3$$ board*

<table>
  {% for row in site.data.impossible-chessboard-N-4 %}
    {% if forloop.first %}
    <tr>
      {% for pair in row %}
        <th>{{ pair[0] }}</th>
      {% endfor %}
    </tr>
    {% endif %}

    {% tablerow pair in row %}
      {{ pair[1] }}
    {% endtablerow %}
  {% endfor %}
</table>
*Example solution for a $$N=4$$ board*

## Conclusion

What we have shown here, is that when a small modification is made to the rules of the original chessboard game, it seems like that winning the game is not impossible after all. A [naive program I wrote](https://gist.github.com/whong92/c99088fbb5928b8af52b1d85514bebd1) managed to solve the game for $$N=3$$ and $$N=4$$. As for whether it is solvable for all $$N \ge 1$$, it is unclear to me at this time, and I believe it will remain an open question, until someone can prove the solvability of a k-coloring of a hypercube graph.

## Appendix

Proof for 

$$
\begin{equation}\label{eq:proof_target}
\frac{2^N}{N} \ge \left \lceil \frac{2^N}{N+1} \right \rceil \quad \forall N \in \mathbb{Z}, N>1
\end{equation}
$$

First, we re-write the above expression ($$\ref{eq:proof_target}$$) into an equivalent form:

$$
\begin{align*}
\left \lfloor \frac{2^N}{N}  \right \rfloor & \ge \frac{2^N}{N+1} \\
\frac{2^N}{N} - \left \{ \frac{2^N}{N} \right \} & \ge \frac{2^N}{N+1}
\end{align*}
$$

Where we have used the notation $$\{x\}$$ equals the fractional portion of the decimal number $$x$$. Aka $$\{x\} = x - \left \lfloor x \right \rfloor$$.

This will give us the following inequality to be proven:

$$
\begin{equation}\label{eq:proof_target_2} 2^N + \frac{2^N}{N} - (N+1) \left \{ \frac{2^N}{N} \right \} \ge 2^N \end{equation}
$$

We will also introduce and prove a lemma that will be useful for our purposes:

$$
\begin{equation} \label{eq:proof_target_6}
\text{Lemma 1}: \frac{2^N}{N} \ge N , \; \forall N>1
\end{equation}
$$

---
**Proof for lemma 1**

By induction. Note the base case $$N=1$$:

$$
I_1: \frac{2^1}{1} = 2 \ge 1 \\
I_N: \frac{2^N}{N} \ge N \\
2^N \ge N^2
$$

$$
\begin{equation} \label{eq:proof_target_3}
2^{N+1} \ge 2N^2
\end{equation}
$$

For all $$N \ge 1$$ the following holds:

$$
N \ge 1\\
(N-1)^2 \ge 0\\
N^2-2N-1 \ge 0
$$

$$
\begin{equation} \label{eq:proof_target_4} 2N^2 \ge N^2 + 2N + 1 = (N+1)^2 \end{equation}
$$

Combining ($$\ref{eq:proof_target_3}$$) and ($$\ref{eq:proof_target_4}$$), gives the following, which completes the proof by induction.

$$
\frac{2^{N+1}}{N+1} \ge N+1 
$$

---

Another identity that will be useful to us is the following, for any pair of integers $$n,m$$ (ref: [wikipedia](https://en.wikipedia.org/wiki/Floor_and_ceiling_functions)):

$$
\begin{equation} \label{eq:proof_target_5} 0 \le \left \{ \frac{m}{n} \right \} \le 1 - \frac{1}{\|n\|} \end{equation}
$$

Combining ($$\ref{eq:proof_target_6}$$) and ($$\ref{eq:proof_target_5}$$) with ($$\ref{eq:proof_target_2}$$), we can write,for $$N>1$$:

$$
\begin{align*}
2^N + \frac{2^N}{N} - (N+1) \left \{ \frac{2^N}{N} \right \} & \ge 2^N + N - (N+1)(1-\frac{1}{N}) \\ 
& = 2^N + N - N - 1 + \frac{N+1}{N}\\
& = 2^N + \frac{1}{N} \ge 2^N
\end{align*}
$$

Which completes the proof.

## References

[3Blue1Brown's Original Video](https://www.youtube.com/watch?v=wTJI_WuZSwE)

[Wikipedia page for useful identities for the ceil and floor functions](https://en.wikipedia.org/wiki/Floor_and_ceiling_functions)