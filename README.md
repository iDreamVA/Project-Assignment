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
### Prim’s algorithm
#### โค้ดฟังก์ชัน
``` python
   def prims_mst(graph_obj):
    if not graph_obj.adj: #เช็คว่าถ้าไม่มีค่าในกราฟให้ return ค่า 0
        return 0, []

    start_node = next(iter(graph_obj.adj)) #ใช้ไล่ทีละตัว ขยับทีละค่า
    min_heap = [] #เก็บค่าทั้งหมด โดยเรียงจากค่า weight จากน้อยไปมาก
    in_mst = {start_node} #ใช้เก็บจุด มีไว้เผื่อเช็ค
    p_mst_edges = [] #เก็บค่าทั้งหมด ที่เลือกไว้ mst
    mst_weight = 0 #เก็บค่าน้ำหนักรวมของ mst

    for neighbor, weight in graph_obj.adj.get(start_node, []): #เช็คว่ามีตัวซ้ำหรือไม่ เพื่อลดการทำงานซับซ้อน
        if neighbor not in in_mst:
            heapq.heappush(min_heap, (weight, neighbor, start_node)) #เรียงค่า weight จากน้อยไปมากและนำไปใส่ใน min_heap

    while min_heap and len(in_mst) < len(graph_obj.adj): #เช็คว่ามีจุดและขอบครบทั้งหมดหรือไม่ ถ้ายังไม่ครบให้วนต่อไป
        weight, u, v = heapq.heappop(min_heap) #เลือกขอบที่มีค่าน้อยที่สุดออกมาก่อน
        if u in in_mst: #ถ้าจุด (ค่า u) อยู่ใน in_mst แล้วให้ข้ามขั้นตอนอื่นๆที่อยู่ใน while ไป
            continue

        in_mst.add(u) #เพิ่มจุด(u)ที่เลือก โดยการเพิ่มลงไปใน in_mst
        mst_weight += weight #บวกค่า weight ที่เลือก ลงค่า mst_weight
        p_mst_edges.append((v, u, weight)) #เก็บค่าขอบที่เลือก โดยการเพิ่มค่าลงไปใน p_mst_edges

        for neighbor, edge_weight in graph_obj.adj.get(u, []): #ใช้เช็คเผื่อไม่ให้เกิดวงจร
            if neighbor not in in_mst:
                heapq.heappush(min_heap, (edge_weight, neighbor, u)) #เมื่อเพิ่มข้อมูลเข้าไปก็จะเรียงใหม่ทันที ตามค่า edge_weight จากน้อยไปมาก

    print("\n--- Prim's MST ---")
    print("Total Weight:", mst_weight)
    print("Edges (u, v, weight):", p_mst_edges)
    return mst_weight, p_mst_edges #คืนค่า
