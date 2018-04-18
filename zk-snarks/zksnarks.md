--- 
title: "zk-SNARKS"
author: |
  | lambda, ThunderDB
  | \url{zeqing.guo@thunderdb.io}
date: "March, 2018"
---

# Homomorphic Encryption

- **Definition:** Homomorphic encryption allows computation on ciphertext which, when decrypted, matches the operations as if they had performed on the plaintext
- An example - RSA: $\mathcal{E}(x_1) \cdot \mathcal{E}(x_2)=\mathcal{E}(x_1 \cdot x_2)$ , so we have $\mathcal{D}(\mathcal{E}(x_1) \cdot \mathcal{E}(x_2))=\mathcal{D}(\mathcal{E}(x_1 \cdot x_2)) =x_1 \cdot x_2$

# Zero-Knowledge Proof

- **Definition: ** zero-knowledge proof is a method by which one party (*prover*) can prove to another party (*verifier*) that she knows a value *x*, without conveying any information apart from the fact that she knows the value *x*.
- zk-SNARKS are short for *succinct non-interactive arguments of knowledge*
    - Succinct: the sizes of the message are tiny
    - Non-interactive: there is no or onlu little interaction
    - Arguments: the verifier is only protected against computationallu limited provers
  
<!-- # NP and Complexity-Theoretic Reductions -->

# P and NP

- Polynomial-time programs: programs whose runtime is at most $n^k$ for some $k$ are called "polynomial-time programs"
- **P** is the class of problems $L$ that have polynomial-time programs
- **NP** is the class of problems $L$ that have a polynomial-time program V that can be used to verify a fact given a polynomially-sized so-called witness for that fact.

# Quadratic Span Programs 

- A **Quadratic Span Program (QSP)** consists of a set of polynomials and the task is to find a linear combination of those that is a multiple of another given polynomials.
- A QSP over a field $F$ for inputs of length $n$ consists of
    - polynomials $v_0, \ldots, v_m, w_0, \ldots, w_m$ over this field $F$,
    - a polynomial $t$ over $F$ (the target polynomials)
    - an injective function $f: {(i, j) | 1 \le i \le n, j \in {0, 1}} \to {1, \ldots, m}$

# Quadratic Span Programs (cont.)

- For each binary input string $u$, the function $f$ restricts the polynomials that can be used. For formally:
    - $a_k, b_k = 1$ if $k = f(i, u[i])$ for some $i$, ($u[i]$ is the $i$th bit of $u$)
    - $a_k, b_k = 0$ if $k = f(i, 1 - u[i])$ for some $i$ 
    - the target polynomials $t$ divides $v_aw_b$ where $v_a = v_0 + a_1v_1 + \ldots + a_mv_m$ and $w_b = w_0 + b_1w_1 + \ldots + b_mw_m$
- If we use $a_1, \ldots, a_m, b_1, \ldots, b_m$ as the *NP witness*, then we can see the that it costs polynomial $t$ for all the verifiers to check that $t$ divides $v_aw_b$
    - This can be facilitated by the prover in providing another polynomial $h$ such that $th = v_aw_b$, so we only need to check $t(s)h(s)-v_a(s)w_b(s) = 0$
    
# The zkSNARKS in Detail

## Background 

- Ellitic curve can be simply represented like $\mathcal{E(x)} := g^x$
- Ellitic curve with pairing function: $e(g^x, g^y)=e(g,g)^{xy}$

# The zkSNARKS in Detail (cont.)

## Setup 

1. Verifier generates Common reference string (CRS)
    - CRS: a random and secret field element($s$) chosen by verifiers
2. Verifier generates $\mathcal{E(s^0)}, \mathcal{E(s^1)}, \cdots, \mathcal{E(s^d)}$, where d is the max degree of all polynomials in the group
3. Verifier generates a random number $\alpha$ and generates $\mathcal{E(\alpha s^0)}, \mathcal{E(\alpha s^1)}, \cdots, \mathcal{E(\alpha s^d)}$
4. Verifier destroys $s$, $\alpha$, $\beta_v$, $\beta_w$, $\gamma$
    - Verifier uses $\alpha$ to check prover evaluated the polynomial correctly by check $e(\mathcal{E(f(s))}, g^{\alpha}) = e(\mathcal{\alpha f(s)}, g)$
$$
\begin{cases}
e(\mathcal{e(f(s))}, g^{\alpha}) & = e(g,g)^{\alpha f(s)}\\
e(\mathcal{\alpha f(s)}, g) & = e(g,g)^{\alpha f(s)}\\
\end{cases}
\rightarrow e(\mathcal{e(f(s))}, g^{\alpha}) = e(\mathcal{\alpha f(s)}, g)
$$

# The zkSNARKS in Detail (cont.)

## Interaction

1. Prover publishes
    - $v_0, \cdots, v_m$ and $w_0, \cdots, w_m$
    - t
2. Verifier publishes
    - $\mathcal{E(s^0)}, \mathcal{E(s^1)}, \cdots, \mathcal{E(s^d)}$ and $\mathcal{E(\alpha s^0)}, \mathcal{E(\alpha s^1)}, \cdots, \mathcal{E(\alpha s^d)}$
    - $\mathcal{E(t(s))}, \mathcal{E(\alpha t(s))}$
    - $\mathcal{E(v_0(s))}, \cdots, \mathcal{E(v_m(s))}, \mathcal{E(\alpha v_0(s))}, \cdots, \mathcal{E(\alpha v_m(s))},$
    - $\mathcal{E(w_0(s))}, \cdots, \mathcal{E(w_m(s))}, \mathcal{E(\alpha w_0(s))}, \cdots, \mathcal{E(\alpha w_m(s))},$
    - $\mathcal{E(\gamma)}, \mathcal{E(\beta_v \gamma)}, \mathcal{E(\beta_w \gamma)}$
    - $\mathcal{E(\beta_v v_1(s))}, \cdots, \mathcal{E(\beta_v v_m(s))}$
    - $\mathcal{E(\beta_w w_1(s))}, \cdots, \mathcal{E(\beta_w w_m(s))}$
    - $\mathcal{E(\beta_v t(s))}, \mathcal(E(\beta_w t(s)))$
    
# The zkSNARKS in Detail (cont.)

## Proof

- The indices that are not restricted in $f: {(i, j) | 1 \le i \le n, j \in {0, 1}} \to {1, \cdots, m}$ with $a_1, \cdots, a_m, b_1, \cdots, b_m$ are called $I_{free}$
- $v_{free}(x)=\sum_{k \in I_{free}}a_kv_k$
- $v_{in}(x)=\sum_{k \in (I \setminus I_{free})}a_kv_k$

Prover generates a proof for verifiers.

- $V_{free}:=\mathcal{E(v_{free}(s))}, W := \mathcal{E(w(s))}, H:=\mathcal{E(h(s))}$
- $V^{\prime}_{free}:=\mathcal{E(\alpha v_{free}(s))}, W^\prime := \mathcal{E(\alpha w(s))}, H^\prime:=\mathcal{E(\alpha h(s))}$
- $Y:=\mathcal{E(\beta_vv_{free}(s) + \beta_ww(s))}$

# The zkSNARKS in Detail (cont.)

## Verification

Verifiers verify the proof as follows:

1. $e(V^{\prime}_{free}, g) = e(V_{free}, g^{\alpha}) e(W^{\prime}, \mathcal{E(1)}) = e(W, \mathcal{E}(\alpha)), e(H^\prime, \mathcal{E(1)}) = e(H, \mathcal{E}(\alpha))$
2. $e(\mathcal{E}(\gamma), Y) = e(\mathcal{E(\beta_v\gamma)}, V_{free})e(\mathcal{E}(\beta_w\gamma),W)$
3. $e(\mathcal{E}(v_0(s)) \mathcal{E}(v_{in}(s))V_{free}, \mathcal{E}(w_0(s))W) = e(H, \mathcal{E}(t(s)))$ 
    - This checks that $(v_0(s)+a_1v_1(s)+\cdots+a_mv_m(s))(w_0(s)+b_1w_1(s)+\cdots+b_mw_m(s)) = h(s)t(s)$
