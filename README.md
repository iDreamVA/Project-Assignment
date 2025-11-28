# Project-Assignment
## Project Description
   1. Spanning Tree using DFS or BFS
   2. Dijkstra’s Algorithm
   3. Minimum Spanning Tree (MST)By Prim and Kruskal
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
      visited = set()  #เก็บจุดที่ถูกเยี่ยมแล้ว (ป้องกันการวนซ้ำ)
      stack = [start_node]  #stack สำหรับ DFS ใช้หลักการ LIFO
      spanning_edges = []  #เก็บขอบที่เลือกลงใน DFS spanning tree
      path = []  #เก็บลำดับการเดินของ DFS
  
      while stack:  #ถ้ายังมี node ใน stack ให้ทำงานต่อ
          u = stack.pop()  #หยิบตัวท้ายสุดออกมา (DFS = ลึกก่อน)
          print(f"[DFS] Visiting node {u}")
  
          if u not in visited:  #ถ้ายังไม่เคยเยี่ยมมาก่อน
              visited.add(u)  #ทำเครื่องหมายว่าเยี่ยมแล้ว
              path.append(u)  #เก็บลำดับที่เดิน
              print(f"[DFS] Visiting node {u}")
  
              neighbors = [v for v, _ in adj.get(u, [])]  #ดึงเฉพาะชื่อเพื่อนบ้านออกมา
  
              for v in reversed(neighbors):  #กลับลำดับเพื่อให้ผลเหมือน DFS recursive
                  if v not in visited:  #เช็คว่าเพื่อนบ้านยังไม่เคยเยี่ยม
                      spanning_edges.append((u, v))  #เพิ่มเป็นขอบของ DFS tree
                      stack.append(v)  #ดันเพื่อนบ้านเข้า stack
                      print(f"[DFS_EDGE] Edge added to spanning tree: ({u}, {v})")
      return spanning_edges, path  #คืนค่าขอบที่เลือก + เส้นทางที่เดิน
```
### BFS
#### โค้ดฟังก์ชัน
``` python
  def bfs_spanning_tree(adj, start_node):
      visited = {start_node}  #เก็บจุดที่เยี่ยมแล้ว เริ่มจาก start
      queue = deque([start_node])  #queue สำหรับ BFS (FIFO)
      spanning_edges = []  #เก็บขอบที่เลือกลงใน BFS spanning tree
      path = [start_node]  #เก็บลำดับที่เดิน BFS
  
      while queue:  #ทำต่อจน queue ว่าง
          u = queue.popleft()  #ดึงตัวหน้าออกก่อน (BFS = กระจายออกเป็นวง)
          print(f"[BFS] Visiting node {u}")
  
          for v, _ in adj.get(u, []):  #เช็คเพื่อนบ้านของ u
              if v not in visited:  #ถ้ายังไม่เคยเยี่ยม
                  visited.add(v)  #ทำเครื่องหมายว่าเยี่ยมแล้ว
                  queue.append(v)  #ใส่เข้าคิวรอการสำรวจ
                  spanning_edges.append((u, v))  #เพิ่มขอบ BFS tree
                  path.append(v)  #เก็บลำดับ BFS
                  print(f"[BFS_EDGE] Edge added to spanning tree: ({u}, {v})")
  
      print(f"[BFS_END] BFS Spanning Tree complete")
      return spanning_edges, path  #คืนค่าขอบ + เส้นทาง BFS
```
### Dijkstra
#### โค้ดฟังก์ชัน
``` python
  def dijkstra(adj, start, end):
      nodes = adj.keys()  #ดึงรายชื่อ node ทั้งหมด
      dist = {i: float("inf") for i in nodes}  #กำหนดระยะเริ่มต้นเป็นอนันต์
      dist[start] = 0  #ระยะทางจาก start ไป start คือ 0
  
      prev = {i: None for i in nodes}  #เก็บว่า node นี้มาจาก node ไหน
      pq = [(0, start)]  #priority queue (min-heap) เริ่มจาก start
  
      while pq:  #ทำงานเรื่อยๆจน heap ว่าง
          d, u = heapq.heappop(pq)  #ดึง node ที่มีระยะน้อยที่สุดออกมา
          if u == end:  #ถ้าถึงจุดหมายแล้ว ให้หยุดได้เลย (เพราะ Dijkstra)
              break
          if d > dist[u]:  #หากข้อมูลใน heapเก่าแล้ว ให้ข้าม
              continue
          for v, weight in adj.get(u, []):  #ไล่ดูเพื่อนบ้าน
              nd = d + weight  #ระยะใหม่ = ระยะปัจจุบัน + น้ำหนักขอบ
              if nd < dist[v]:  #ถ้าเจอระยะที่ดีกว่า
                  dist[v] = nd  #อัปเดตระยะ
                  prev[v] = u  #บันทึกเส้นทาง
                  heapq.heappush(pq, (nd, v))  #ดันเข้ากองสำหรับพิจารณาในอนาคต
  
      #เช็คว่ามีทางไปถึงปลายทางไหม
      if dist[end] == float("inf"):
          return None, float("inf"), []  #ไม่มีเส้นทาง
  
      #สร้างเส้นทางย้อนหลัง (backtrack)
      path = []
      cur = end
      while cur:
          path.append(cur)
          cur = prev[cur]
      path.reverse()  #กลับลำดับให้ถูกต้อง
      #สร้าง list ของขอบตามเส้นทาง
      path_edges = [(path[i], path[i+1]) for i in range(len(path)-1)]
      return path, dist[end], path_edges  #คืนค่า path, ระยะ, และขอบ
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
### Dijkstra
#### โค้ดฟังก์ชัน
``` python
  def draw_graph(graph_object):
      import networkx as nx  #ใช้สร้างกราฟสำหรับวาดภาพ
      import matplotlib.pyplot as plt  #ใช้ในการ plot รูป
  
      G = nx.Graph()  #สร้างกราฟเปล่า (undirected)
  
      #เพิ่มโหนดทั้งหมดเข้าไปในภาพ
      for node in graph_object.adj:
          G.add_node(node)  #เพิ่มทีละ node
  
      #เพิ่มขอบทั้งหมด (edges) แบบไม่ให้ซ้ำ
      seen = set()  #เอาไว้กันการเพิ่มขอบซ้ำ (เพราะกราฟแบบ list อาจมี u→v และ v→u)
      for u in graph_object.adj:
          for v, w in graph_object.adj[u]:
              if (v, u) not in seen:  #เช็คว่าเคยเพิ่มขอบนี้หรือยัง
                  G.add_edge(u, v, weight=w)  #เพิ่มขอบพร้อมใส่น้ำหนัก
                  seen.add((u, v))  #เก็บว่าเพิ่มแล้ว เพื่อป้องกันซ้ำ
  
      # layout = ตำแหน่งการจัดวางโหนด (เพื่อให้ภาพดูดีและอ่านง่าย)
      pos = nx.spring_layout(G, seed=42)  
      # spring_layout = กระจายโหนดให้ห่างกันแบบแรงผลัก–แรงดึง (ดูสมดุลที่สุด)
  
      plt.figure(figsize=(8, 6))  #กำหนดขนาดรูป
  
      #วาดโหนดทั้งหมด
      nx.draw_networkx_nodes(G, pos, node_size=500, node_color="#ADD8E6")
      # node_size = ความใหญ่จุด
      # node_color = สีของ node
      #วาดเส้นขอบทั้งหมด
      nx.draw_networkx_edges(G, pos, width=2) # width = ความหนาของเส้นเชื่อม node
      #วาด label ของโหนด
      nx.draw_networkx_labels(G, pos, font_size=12, font_color="black") #ดึงน้ำหนัก edges มาแสดงบนเส้น
      edge_weights = nx.get_edge_attributes(G, 'weight')
      nx.draw_networkx_edge_labels(G, pos, edge_labels=edge_weights, font_size=10) # edge_labels = dictionary { (u,v): weight }
  
      plt.title("Graph Visualization")  #ชื่อหัวรูป
      plt.axis("off")  #ปิดแกน เพื่อให้ภาพดูสะอาด
      plt.tight_layout()  #จัด spacing ของรูปให้พอดี
      plt.show()  #แสดงรูป
```