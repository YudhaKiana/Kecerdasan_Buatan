import random
from collections import deque

# ==============================================================================
# BAGIAN 1: FUNGSI-FUNGSI PENYELESAIAN LABIRIN
# ==============================================================================

def generate_random_maze(width=15, height=7):
    """
    Membuat labirin acak yang dijamin bisa diselesaikan.
    """
    while True:
       
        maze = [[' ' for _ in range(width)] for _ in range(height)]
        
        
        for y in range(height):
            for x in range(width):
                if random.random() < 0.3: # 30% kemungkinan ada dinding
                    maze[y][x] = '#'
        
       
        start_pos = (0, 0)
        end_pos = (height - 1, width - 1)
        maze[start_pos[0]][start_pos[1]] = 'S'
        maze[end_pos[0]][end_pos[1]] = 'E'
        
       
        if solve_maze_bfs(maze, start_pos, end_pos):
            return maze, start_pos, end_pos

def solve_maze_dfs(maze, start, end):
    """Menyelesaikan labirin menggunakan DFS (menemukan satu jalur)."""
    rows, cols = len(maze), len(maze[0])
    stack = [(start, [start])]
    visited = {start}

    while stack:
        (x, y), path = stack.pop()
        if (x, y) == end:
            return path
        for dx, dy in [(-1, 0), (1, 0), (0, -1), (0, 1)]:
            nx, ny = x + dx, y + dy
            if 0 <= nx < rows and 0 <= ny < cols and maze[nx][ny] != '#' and (nx, ny) not in visited:
                visited.add((nx, ny))
                stack.append(((nx, ny), path + [(nx, ny)]))
    return None

def solve_maze_bfs(maze, start, end):
    """Menyelesaikan labirin menggunakan BFS (menemukan jalur terpendek)."""
    rows, cols = len(maze), len(maze[0])
    queue = deque([(start, [start])])
    visited = {start}

    while queue:
        (x, y), path = queue.popleft()
        if (x, y) == end:
            return path
        for dx, dy in [(-1, 0), (1, 0), (0, -1), (0, 1)]:
            nx, ny = x + dx, y + dy
            if 0 <= nx < rows and 0 <= ny < cols and maze[nx][ny] != '#' and (nx, ny) not in visited:
                visited.add((nx, ny))
                queue.append(((nx, ny), path + [(nx, ny)]))
    return None

def print_maze_solution(maze, path):
    """Mencetak labirin dengan jalur solusi yang ditandai '*'."""
    solution_maze = [list(row) for row in maze]
    if path:
        for r, c in path:
            if solution_maze[r][c] == ' ':
                solution_maze[r][c] = '*'
    for row in solution_maze:
        print(" ".join(row))

# ==============================================================================
# BAGIAN 2: FUNGSI-FUNGSI PENCARIAN RUTE PADA GRAF
# ==============================================================================

def bfs_shortest_path_graph(graph, start, end):
    """Menemukan rute terpendek di graf menggunakan BFS."""
    queue = deque([(start, [start])])
    visited = {start}
    while queue:
        current_node, path = queue.popleft()
        for neighbor in graph.get(current_node, []):
            if neighbor not in visited:
                if neighbor == end:
                    return path + [neighbor]
                visited.add(neighbor)
                queue.append((neighbor, path + [neighbor]))
    return None

def find_all_paths_dfs_graph(graph, start, end, path=[]):
    """Menemukan semua kemungkinan rute di graf menggunakan DFS."""
    path = path + [start]
    if start == end: return [path]
    if start not in graph: return []
    all_paths = []
    for neighbor in graph[start]:
        if neighbor not in path:
            new_paths = find_all_paths_dfs_graph(graph, neighbor, end, path)
            for p in new_paths:
                all_paths.append(p)
    return all_paths

# ==============================================================================
# BAGIAN 3: PROGRAM UTAMA DAN MENU INTERAKTIF
# ==============================================================================

def main():
    """Fungsi utama untuk menampilkan menu dan menjalankan pilihan pengguna."""
    graf_sampel = {
        'A': ['B', 'C'], 'B': ['D', 'E'], 'C': ['E'], 'D': ['E'], 'E': []
    }

    while True:
        print("\n" + "="*40)
        print("    PILIH PROGRAM YANG AKAN DIJALANKAN")
        print("="*40)
        print("  [1] Selesaikan Labirin Acak (DFS)")
        print("  [2] Selesaikan Labirin Acak (BFS - Terpendek)")
        print("  [3] Cari Rute Terpendek di Graf (BFS)")
        print("  [4] Cari Semua Rute di Graf (DFS)")
        print("  [0] Keluar")
        print("="*40)
        
        try:
            pilihan = int(input("Masukkan pilihan Anda: "))
        except ValueError:
            print("Input tidak valid! Harap masukkan angka.")
            continue

        print("\n--- HASIL ---")

        if pilihan == 1:
            print("🎲 Membuat labirin acak baru...")
            labirin, start_pos, end_pos = generate_random_maze()
            print("Labirin yang Dihasilkan:")
            print_maze_solution(labirin, None)
            print("\nMenyelesaikan dengan DFS...")
            path = solve_maze_dfs(labirin, start_pos, end_pos)
            if path:
                print("✅ Satu jalur ditemukan:")
                print_maze_solution(labirin, path)
            else:
                print("❌ Tidak ada solusi.")

        elif pilihan == 2:
            print("🎲 Membuat labirin acak baru...")
            labirin, start_pos, end_pos = generate_random_maze()
            print("Labirin yang Dihasilkan:")
            print_maze_solution(labirin, None)
            print("\nMenyelesaikan dengan BFS (Jalur Terpendek)...")
            path = solve_maze_bfs(labirin, start_pos, end_pos)
            if path:
                print("✅ Jalur terpendek ditemukan:")
                print_maze_solution(labirin, path)
            else:
                print("❌ Tidak ada solusi.")

        elif pilihan == 3:
            print("Mencari rute terpendek di graf (A ke E) dengan BFS...")
            path = bfs_shortest_path_graph(graf_sampel, 'A', 'E')
            if path: print(f"✅ Rute terpendek: {' -> '.join(path)}")
            else: print("❌ Tidak ada rute.")

        elif pilihan == 4:
            print("Mencari semua rute di graf (A ke E) dengan DFS...")
            paths = find_all_paths_dfs_graph(graf_sampel, 'A', 'E')
            if paths:
                print("✅ Ditemukan jalur-jalur berikut:")
                for i, p in enumerate(paths):
                    print(f"  Jalur {i+1}: {' -> '.join(p)}")
            else: print("❌ Tidak ada rute.")
        
        elif pilihan == 0:
            print("Terima kasih! Program selesai.")
            break
        
        else:
            print("Pilihan tidak valid! Silakan coba lagi.")
        
        input("\nTekan Enter untuk kembali ke menu...")

if __name__ == "__main__":
    main()
