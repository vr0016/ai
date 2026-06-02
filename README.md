1. ALPHA - BETA PRUNING

import math
def alphabeta(depth, node, maxi, values, alpha, beta):
    if depth == 3:
        return values[node]
    best = -math.inf if maxi else math.inf

    for i in range(2):
        val = alphabeta(depth + 1, node * 2 + i, not maxi, values, alpha, beta)
        if maxi:
            best = max(best, val)
            alpha = max(alpha, best)
        else:
            best = min(best, val)
            beta = min(beta, best)
        if alpha >= beta:
            print("Pruned at node:", node)
            break
    return best

values = [3, 5, 6, 9, 1, 2, 0, -1]
res = alphabeta(0, 0, True, values, -math.inf, math.inf)
print("Optimal value:", res)





2. 8 – PUZZLE

import heapq, numpy as np
s=(1,2,3,4,0,5,7,8,6)
g=(1,2,3,4,5,6,7,8,0)
gp={v:(i//3,i%3) for i,v in enumerate(g)}

h=lambda s:sum(abs(i//3-gp[v][0])+abs(i%3-gp[v][1]) for i,v in enumerate(s) if v!=0)
def n(s):
    z=s.index(0); r,c=divmod(z,3); res=[]
    for dr,dc in [(-1,0),(1,0),(0,-1),(0,1)]:
        nr,nc=r+dr,c+dc
        if 0<=nr<3 and 0<=nc<3:
            nz=nr*3+nc; t=list(s)
            t[z],t[nz]=t[nz],t[z]
            res.append(tuple(t))
    return res

def solve(s):
    pq=[(h(s),0,s,[])]
    v=set()
    while pq:
        _,c,x,p=heapq.heappop(pq)
        if x==g: return p+[x]
        if x in v: continue
        v.add(x)
        for nx in n(x):
            if nx not in v:
                heapq.heappush(pq,(c+1+h(nx),c+1,nx,p+[x]))
sol=solve(s)                 #below def
if sol:
    for x in sol: print(np.array(x).reshape(3,3),"\n")
else:
    print("No solution found.")


3. TOWER OF HANOI

def tower_of_hanoi(n, s, a, t):
    if n == 1:
        print(f"Move disk 1 from {s} to {t}")
        return
    tower_of_hanoi(n-1, s, t, a)
    print(f"Move disk {n} from {s} to {t}")
    tower_of_hanoi(n-1, a, s, t)

n = int(input("Enter number of disks: "))
tower_of_hanoi(n, "A", "B", "C")
print("Total Moves:", 2**n - 1)


4. MONKEY BANANA

from collections import deque
def monkey_banana():
    s = ('Far-Chair', 'Chair-Not-Under-Banana', 'Off-Chair', 'Empty')
    g = ('Near-Chair', 'Chair-Under-Banana', 'On-Chair', 'Holding')
    a = {
        "Move to Chair": lambda x: ('Near-Chair', *x[1:]) if x[0] != 'Near-Chair' else None,
        "Push Chair under Banana": lambda x: ('Near-Chair', 'Chair-Under-Banana', x[2], x[3]) if x[0] == 'Near-Chair' else None,
        "Climb Chair": lambda x: ('Near-Chair', 'Chair-Under-Banana', 'On-Chair', x[3]) if x[1] == 'Chair-Under-Banana' else None,
        "Grasp Banana": lambda x: g if x[:3] == g[:3] else None
    }

    q, v = deque([(s, [])]), set()

    while q:
        x, p = q.popleft()
        if x == g:
            print("Solution Found!\n")
            print(*p, sep="\n")
            print("\nFinal State:", x)
            return

        if x not in v:
            v.add(x)
            for n, f in a.items():
                y = f(x)
                if y:
                    q.append((y, p + [n]))

monkey_banana()




5. 8 – QUEENS

def print_board(b):
    for r in b:
        print(*['Q' if x else '.' for x in r])
    print()

def safe(b, r, c, n):
    return all(
        not b[i][c] and
        (c-r+i < 0 or not b[i][c-r+i]) and
        (c+r-i >= n or not b[i][c+r-i])
        for i in range(r) )

def solve(b, r, n):
    if r == n:
        print_board(b)
        return True                                                  
    found = False                                                                #omit this for one solution only
    for c in range(n):
        if safe(b, r, c, n):
            b[r][c] = 1
            found = solve(b, r + 1, n) or found             #instead write if solve(b,r+1,n) return True
            b[r][c] = 0
    return found

n = 8
board = [[0] * n for _ in range(n)]
print("Solutions printed above." if solve(board, 0, n) else "No solution exists.")
6. TRAVELLING SALESPERSON

import heapq
g = {
    'A': {'A':0,'B':15,'C':20,'D':25,'E':10},
    'B': {'A':15,'B':0,'C':35,'D':25,'E':35},
    'C': {'A':20,'B':35,'C':0,'D':30,'E':45},
    'D': {'A':25,'B':25,'C':30,'D':0,'E':30},
    'E': {'A':10,'B':35,'C':45,'D':30,'E':0}}

c = list(g)
n = len(c)
m = min(g[i][j] for i in c for j in c if i != j)

mat = [[g[i][j] for j in c] for i in c]
pq = [(0,0,0,[0])]

while pq:
    _,cost,u,p = heapq.heappop(pq)
    if len(p) == n:
        print("Optimal Path:", " -> ".join(c[i] for i in p+[0]))
        print("Minimum Cost:", cost + mat[u][0])
        break

    for v in range(n):
        if v not in p:
            np = p+[v]
            nc = cost + mat[u][v]
            heapq.heappush(pq,(nc + m*(n-len(np)), nc, v, np))
