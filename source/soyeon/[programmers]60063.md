# 프로그래머스 문제
- 블록 이동하기
    - https://programmers.co.kr/learn/courses/30/lessons/60063

## 풀이 방법
### 문제 이해하기
- 2X1 크기의 로봇이 회전하면서 움직이는데 (N,N)까지 도착하는데 최소시간을 구하는 문제이다.

### 문제 접근 방법
- 중복되지 않게 Hashset에 robot을 넣어서 움직인다.

### 접근 방법을 적용한 코드
```java
class Solution {
	public static int n;
	public static int mat[][];
	public static HashSet<Robot> visited;
	public static int dc[] = { 0, 0, 1, -1 };
	public static int dr[] = { 1, -1, 0, 0 };
	public static int dth[] = { -1, 1 };

	public int solution(int[][] board) {
		n = board.length;
		mat = new int[n+1][n+1];
		for(int i=1;i<=n;i++) {
			for(int j=1;j<=n;j++) {
				mat[i][j]=board[i-1][j-1];
			}
		}
		visited = new HashSet<Robot>();
		Queue<Robot> queue = new LinkedList<Robot>();
		queue.add(new Robot(1, 1, 1, 2));
		return pipe(queue, 0);
	}

	public static int pipe(Queue<Robot> queue, int ans) {
		Queue<Robot> nextQueue = new LinkedList<Robot>();
		while (!queue.isEmpty()) {
			Robot robot = queue.poll();

			if (isFinished(robot)) {
				return ans;
			}

     
			for (int i = 0; i < 4; i++) { // move
				Robot nextRobot = move(robot, i);
				if (nextRobot.isSuccessful && !visited.contains(nextRobot)) {
					nextQueue.add(nextRobot);
					visited.add(nextRobot);
				}
			}

      
			if (robot.r1 == robot.r2) { // rotation
				for (int i = 0; i < 2; i++) {
					int r1 = robot.r1 + dth[i];
					int c1 = robot.c1;
					int r2 = robot.r2 + dth[i];
					int c2 = robot.c2;

					if (isBoundary(r1, c1) && mat[r1][c1] != 1) {
						Robot nextRobot = new Robot(robot.r1, robot.c1, r1, c1);
						if (!visited.contains(nextRobot)) {
							nextQueue.add(nextRobot);
							visited.add(nextRobot);
						}
					}
					if (isBoundary(r2, c2) && mat[r2][c2] != 1) {
						Robot nextRobot = new Robot(robot.r2, robot.c2, r2, c2);
						if (!visited.contains(nextRobot)) {
							nextQueue.add(nextRobot);
							visited.add(nextRobot);
						}
					}
				}
			} else if (robot.c1 == robot.c2) {
				for (int i = 0; i < 2; i++) {
					int r1 = robot.r1;
					int c1 = robot.c1 + dth[i];
					int r2 = robot.r2;
					int c2 = robot.c2 + dth[i];

					if (isBoundary(r1, c1) && mat[r1][c1] != 1) {
						Robot nextRobot = new Robot(robot.r1, robot.c1, r1, c1);
						if (!visited.contains(nextRobot)) {
							nextQueue.add(nextRobot);
							visited.add(nextRobot);
						}
					}
					if (isBoundary(r2, c2) && mat[r2][c2] != 1) {
						Robot nextRobot = new Robot(robot.r2, robot.c2, r2, c2);
						if (!visited.contains(nextRobot)) {
							nextQueue.add(nextRobot);
							visited.add(nextRobot);
						}
					}
				}
			}
		}
		return pipe(nextQueue, ans+1);
	}

	public static boolean isFinished(Robot robot) {
		if (robot.c1 == n && robot.r1 == n)
			return true;
		if (robot.c2 == n && robot.r2 == n)
			return true;
		return false;
	}

	public static Robot move(Robot robot, int direction) {
		int r1 = robot.r1 + dr[direction];
		int r2 = robot.r2 + dr[direction];
		int c1 = robot.c1 + dc[direction];
		int c2 = robot.c2 + dc[direction];
		Robot nextRobot = new Robot(r1, c1, r2, c2);
		if (isBoundary(r1, c1) && isBoundary(r2, c2)) {
			if (mat[r1][c1] != 1 && mat[r2][c2] != 1) {
				nextRobot.successed();
				return nextRobot;
			}
		}
		nextRobot.failed();
		return nextRobot;
	}

	public static boolean isBoundary(int r, int c) {
		if (r > n)
			return false;
		if (c > n)
			return false;
		if (r == 0)
			return false;
		if (c == 0)
			return false;
		return true;
	}
}

class Robot {
	int c1, c2, r1, r2;
	boolean isSuccessful;

	public Robot(int r1, int c1, int r2, int c2) {
		this.c1 = c1;
		this.r1 = r1;
		this.c2 = c2;
		this.r2 = r2;
		isSuccessful = true;
	}

	public void failed() {
		this.isSuccessful = false;
	}

	public void successed() {
		this.isSuccessful = true;
	}
}
```

### 해결하지 못한 이유
- 시간 초과(디버깅 결과 HashSet에 동일한 객체가 들어있음을 확인함)
- rotate 시 대각선 체크 

### 문제를 해결한 코드
```java
import java.util.*;
public class Main {
	public static void main(String[] args) {
		Solution solution = new Solution();
		int board[][] = {{0, 0, 0, 1, 1},{0, 0, 0, 1, 0},{0, 1, 0, 1, 1},{1, 1, 0, 0, 1},{0, 0, 0, 0, 0}};
		System.out.println(solution.solution(board));
	}
}

class Solution {
	public static int n;
	public static int mat[][];
	public static HashSet<Robot> visited;
	public static int dc[] = { 0, 0, 1, -1 };
	public static int dr[] = { 1, -1, 0, 0 };
	public static int dth[] = { -1, 1 };

	public int solution(int[][] board) {
		n = board.length;
		mat = new int[n+1][n+1];
		for(int i=1;i<=n;i++) {
			for(int j=1;j<=n;j++) {
				mat[i][j]=board[i-1][j-1];
			}
		}
		visited = new HashSet<Robot>();
		Queue<Robot> queue = new LinkedList<Robot>();
		queue.add(new Robot(1, 1, 1, 2));
		return pipe(queue, 0);
	}

	public static int pipe(Queue<Robot> queue, int ans) {
		Queue<Robot> nextQueue = new LinkedList<Robot>();
		while (!queue.isEmpty()) {
			Robot robot = queue.poll();

			if (isFinished(robot)) {
				//System.out.println(visited);
				return ans;
			}
			
			// move
			for (int i = 0; i < 4; i++) {
				int r1 = robot.r1 + dr[i];
				int r2 = robot.r2 + dr[i];
				int c1 = robot.c1 + dc[i];
				int c2 = robot.c2 + dc[i];
				Robot nextRobot = new Robot(r1, c1, r2, c2);
				if (isBoundary(r1, c1) && isBoundary(r2, c2)) {
					if (mat[r1][c1] != 1 && mat[r2][c2] != 1) {
						if(!visited.contains(nextRobot)) {
							nextQueue.add(nextRobot);
							visited.add(nextRobot);
						}
					}
				}
			}

			// rotate
			if (robot.r1 == robot.r2) {
				for (int i = 0; i < 2; i++) {
					int r1 = robot.r1 + dth[i];
					int c1 = robot.c1;
					int r2 = robot.r2 + dth[i];
					int c2 = robot.c2;

					if (isBoundary(r1, c1) && mat[r1][c1] != 1 && mat[r2][c2] != 1) {
						Robot nextRobot = new Robot(robot.r1, robot.c1, r1, c1);
						if (!visited.contains(nextRobot)) {
							nextQueue.add(nextRobot);
							visited.add(nextRobot);
						}
					}
					if (isBoundary(r2, c2) && mat[r1][c1] != 1 && mat[r2][c2] != 1) {
						Robot nextRobot = new Robot(robot.r2, robot.c2, r2, c2);
						if (!visited.contains(nextRobot)) {
							nextQueue.add(nextRobot);
							visited.add(nextRobot);
						}
					}
				}
			} else if (robot.c1 == robot.c2) {
				for (int i = 0; i < 2; i++) {
					int r1 = robot.r1;
					int c1 = robot.c1 + dth[i];
					int r2 = robot.r2;
					int c2 = robot.c2 + dth[i];

					if (isBoundary(r1, c1) && mat[r1][c1] != 1 && mat[r2][c2] != 1) {
						Robot nextRobot = new Robot(robot.r1, robot.c1, r1, c1);
						if (!visited.contains(nextRobot)) {
							nextQueue.add(nextRobot);
							visited.add(nextRobot);
						}
					}
					if (isBoundary(r2, c2) && mat[r1][c1] != 1 && mat[r2][c2] != 1) {
						Robot nextRobot = new Robot(robot.r2, robot.c2, r2, c2);
						if (!visited.contains(nextRobot)) {
							nextQueue.add(nextRobot);
							visited.add(nextRobot);
						}
					}
				}
			}
		}
		return pipe(nextQueue, ans+1);
	}

	public static boolean isFinished(Robot robot) {
		if (robot.c1 == n && robot.r1 == n)
			return true;
		if (robot.c2 == n && robot.r2 == n)
			return true;
		return false;
	}

	public static boolean isBoundary(int r, int c) {
		if (r > n)
			return false;
		if (c > n)
			return false;
		if (r == 0)
			return false;
		if (c == 0)
			return false;
		return true;
	}
}

class Robot {
	int c1, c2, r1, r2;

	public Robot(int r1, int c1, int r2, int c2) {
		this.c1 = c1;
		this.r1 = r1;
		this.c2 = c2;
		this.r2 = r2;
	}
	@Override
	public boolean equals(Object obj) {
		Robot robot = (Robot)obj;
		if(this.r1 == robot.r1 && this.c1 == robot.c1 && this.r2 == robot.r2 && this.c2 == robot.c2) return true;
		if(this.r1 == robot.r2 && this.c1 == robot.c2 && this.r2 == robot.r1 && this.c2 == robot.c1) return true;
		return false;
	}
    @Override
    public int hashCode() {
        return (Math.min(r1, r2)+" "+Math.max(r1,r2)+" "+Math.min(c1, c2)+" "+Math.max(c1, c2)).hashCode();
    }
	public String toString() {
		return r1+" "+c1+" "+r2+" "+c2+"\n";
	}
}
```

### 문제를 해결한 방법
- [객체 내부에 hashcode와 equals 함수 직접 구현](https://dydtjr1128.github.io/java/2019/04/25/JAVA-Hash.html)
