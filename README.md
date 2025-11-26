# Project-Assignment
## Project Description
   1. Spanning Tree using DFS or BFS
   2. Dijkstra’s Algorithm
   3. Minimum Spanning Tree (MST) ฺBy Prim and Kruskal
## Installation
  สามารถเปิดไฟล์ใน Google Colab แล้วกดรันทั้งหมดได้เลย

## วิธีใช้งานโปรแกรม
  1. Run the program
  2. ป้อนข้อมูลเส้นเชื่อมของกราฟ และเมื่อป้อนครบให้พิมพ์ done ตามรูปแบบดังนี้
      Input Format :
          u v weight
      Example:
          A B 3
          A C 2
          C D 4
          done
  3. ป้อนจุดเริ่มต้นสำหรับการทำ DFS/BFS
  4. ป้อนจุดเริ่มและปลายทางสำหรับ Dijkstra
  5. โปรแกรมจะแสดงผลลัพธ์ดังนี้
      - DFS Spanning Tree
      - BFS Spanning Tree
      - Shortest Path (Dijkstra)
      - Prim’s MST
      - Kruskal’s MST
      - กราฟ

## อธิบาย code หลัก
### input
#### โค้ดฟังก์ชัน
``` python
   def read_graph_input():
    G = Graph()  # สร้างกราฟว่าง
    print("Enter edges: u v weight (type 'done' to finish)")
    #ลูปรับอินพุตทีละบรรทัด
    while True:
        line = input(">> ").strip()  # อ่านอินพุตและตัดช่องว่างเกิน
        if line.lower() == "done":  # ถ้าพิมพ์ 'done' ออกลูป
            break
        try:
            # แยกค่า u, v และ weight
            u, v, weight = line.split()
            # แปลง weight เป็น int หรือ float ตามค่าที่ป้อน
            weight = float(weight) if '.' in weight else int(weight)
        except:
            print("[ERROR] Format must be: u v weight")
            continue
        # เพิ่มขอบลงกราฟ ตรวจสอบขอบซ้ำ
        if not G.add_edge(u, v, weight):
            print(f"[DUPLICATE] Edge ({u},{v}) exists")
            continue
        print(f"[ADD_EDGE] Added edge: ({u},{v}) weight {weight}")    
    print("[INPUT_END] Finished reading edges")
    return G
```
### DFS
#### โค้ดฟังก์ชัน
``` python
   def dfs_spanning_tree(adj, start_node):
    # เริ่ม DFS จากnodeที่กำหนด
    print(f"[DFS_START] Starting DFS from node {start_node}")   
    visited = {start_node}       # เก็บnodeที่เยี่ยมชมแล้ว
    stack = [start_node]         # stackสำหรับ DFS
    spanning_edges = []          # รวมขอบใน spanning tree
    while stack:                 # ทำจนกว่าstackจะว่าง
        u = stack.pop()          # เอาnodeล่าสุดออกจากstack
        print(f"[DFS] Visiting node {u}")        
        # ตรวจสอบขอบทั้งหมดของโหนด u
        for v, weight in adj.get(u, []):
            if v not in visited:             # ถ้าnode v ยังไม่ถูกเยี่ยมชม
                visited.add(v)               # ทำเครื่องหมายว่าเยี่ยมชมแล้ว
                stack.append(v)              # เพิ่ม v เข้า stack เพื่อ DFS ต่อ
                spanning_edges.append((u, v, weight))  # เพิ่มขอบนี้ลง spanning tree
                print(f"[DFS_EDGE] Edge added to spanning tree: ({u}, {v}, weight {weight})")
    print(f"[DFS_END] DFS Spanning Tree complete")
    return spanning_edges       # คืนค่า list ของขอบใน DFS spanning tree
```
### Kruskal’s algorithm
#### โค้ดฟังก์ชัน
``` python
   def kruskal_mst(G):
    # คลาสสำหรับกันการเกิดcycle
    class UnionFind:
        def __init__(self, nodes):
            self.parent = {node: node for node in nodes} # parent ของแต่ละโหนดเริ่มต้นเป็นตัวเอง        
        def find(self, i):
            if self.parent[i] != i:  # หารากของโหนด i 
                self.parent[i] = self.find(self.parent[i])
            return self.parent[i]
        def union(self, i, j):
            root_i = self.find(i)
            root_j = self.find(j)
            if root_i != root_j: # รวมสองเซ็ต หากรากต่างกัน
                self.parent[root_i] = root_j
                return True  # ไม่เกิดcycle
            return False     # เกิดcycle
    # เก็บขอบทั้งหมดโดยไม่ซ้ำ
    all_edges = []
    processed_edges = set()
    for u in G.adj:
        for v, weight in G.adj[u]:
            edge = tuple(sorted((u, v)))  # เพื่อป้องกันขอบซ้ำ (u,v) กับ (v,u)
            if edge not in processed_edges:
                all_edges.append((weight, u, v))
                processed_edges.add(edge)
    all_edges.sort() # เรียงขอบตามน้ำหนักจากน้อยไปมาก
    nodes = list(G.adj.keys())
    dsu = UnionFind(nodes)
    # ตัวแปรเก็บ MST และน้ำหนักรวม
    k_mst_edges = []
    total_weight = 0

    # เลือกขอบตามน้ำหนักจากน้อยไปมาก
    for weight, u, v in all_edges:
        if dsu.union(u, v):  # ถ้าเชื่อมแล้วไม่เกิด cycle
            k_mst_edges.append((u, v, weight))  # เพิ่มขอบลง MST
            total_weight += weight               # รวมค่าน้ำหนัก
            if len(k_mst_edges) == len(nodes) - 1:  # มี n-1 หยุด
                break
    # แสดงผล MST
    print("\n--- Kruskal's MST ---")
    print(f"Total Weight: {total_weight}")
    print(f"Edges (u, v, weight):{k_mst_edges}")
    return k_mst_edges, total_weight  # คืนค่า MST และน้ำหนักรวม
```
