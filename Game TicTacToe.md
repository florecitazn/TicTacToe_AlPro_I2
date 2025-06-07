import java.util.Scanner;

public class TicTacToe {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        boolean playAgain = true;

        while (playAgain) {
            System.out.println("=== Tic Tac Toe - Player vs Komputer (Hard) ===");
            System.out.print("Ingin bermain duluan? (y/n): ");
            String firstMove = scanner.next();

            boolean playerTurn = firstMove.equalsIgnoreCase("y");
            Board board = new Board(playerTurn ? 1 : -1);

            while (!board.gameOver()) {
                if (playerTurn) {
                    board.disp();
                    PlayerMove(board);
                } else {
                    System.out.println("Giliran Player ('O'):");
                    int[] move = StrategyCompWin.getBestMove(board);
                    if (move != null) {
                        board.setBoard(move[0], move[1]);
                    }
                }
                playerTurn = !playerTurn;
            }

            board.disp();

            // Tampilkan hasil akhir
            if (board.winner() == 1) {
                System.out.println("Pemain 'O' (Player) menang!");
            } else if (board.winner() == -1) {
                System.out.println("Pemain 'X' (Komputer) menang!");
            } else {
                System.out.println("Permainan seri!");
            }

            // Main ulang?
            System.out.print("Ingin main lagi? (y/n): ");
            String jawaban = scanner.next();
            if (!jawaban.equalsIgnoreCase("y")) {
                playAgain = false;
            }
        }

        scanner.close();
        System.out.println("Terima kasih telah bermain!");
    }

    private static void PlayerMove(Board board) {
        Scanner scanner = new Scanner(System.in);
        System.out.println("Giliran Player: " + board.getTurn());
        System.out.print("Masukkan baris (0-2): ");
        int baris = scanner.nextInt();
        System.out.print("Masukkan kolom (0-2): ");
        int kolom = scanner.nextInt();

        if (baris < 0 || baris > 2 || kolom < 0 || kolom > 2 || !board.setBoard(baris, kolom)) {
            System.out.println("Posisi tidak valid! Silakan coba lagi.");
            PlayerMove(board);
        }
    }
}

class StrategyCompWin {
    public static int[] getBestMove(Board board) {
        int[][] data = board.getData();

        int[] winMove = findWinningMove(data, -1);
        if (winMove != null) return winMove;

        int[] blockMove = findWinningMove(data, 1);
        if (blockMove != null) return blockMove;

        if (data[1][1] == 0) return new int[]{1, 1};

        int[][] corners = {{0,0}, {0,2}, {2,0}, {2,2}};
        for (int[] corner : corners) {
            if (data[corner[0]][corner[1]] == 0) {
                return corner;
            }
        }

        for (int i = 0; i < 3; i++) {
            for (int j = 0; j < 3; j++) {
                if (data[i][j] == 0) {
                    return new int[]{i, j};
                }
            }
        }

        return null;
    }

    private static int[] findWinningMove(int[][] data, int player) {
        for (int i = 0; i < 3; i++) {
            for (int j = 0; j < 3; j++) {
                if (data[i][j] == 0) {
                    data[i][j] = player;
                    if (checkWinForPlayer(data, player)) {
                        data[i][j] = 0;
                        return new int[]{i, j};
                    }
                    data[i][j] = 0;
                }
            }
        }
        return null;
    }

    private static boolean checkWinForPlayer(int[][] data, int player) {
        for (int i = 0; i < 3; i++) {
            if (data[i][0] == player && data[i][1] == player && data[i][2] == player) return true;
            if (data[0][i] == player && data[1][i] == player && data[2][i] == player) return true;
        }

        if (data[0][0] == player && data[1][1] == player && data[2][2] == player) return true;
        if (data[0][2] == player && data[1][1] == player && data[2][0] == player) return true;

        return false;
    }
}

class Board {
    private int[][] data;
    private int turn;

    public Board(int turn){  //constructor, membuat papan game
        this.data = new int[3][3];
        this.turn = turn;
    }

    public void disp(){ //menampilkan papan ke output pengguuns
        for(int i=0; i<3; i++){
            for(int j=0;j<3;j++){
                switch(this.data[i][j]){
                    case 0  -> System.out.print("  -  ");
                    case -1 -> System.out.print("  X  ");
                    case 1  -> System.out.print("  O  ");
                }
            }
            System.out.println();
        }
        System.out.println("\n\n");
    }

    public boolean setBoard(int brs, int kol){
        if(this.data[brs][kol]==0){
            this.data[brs][kol] = turn; //isi kotak sesuai giliran
            turn = - turn; //ganti giliran
            return true;
        } else {
            return false;
        } //di sini cek apakah line udah ada yang isi
    }

    public int winner() {
        for (int i = 0; i < 3; i++) {
            if (this.data[i][0] == this.data[i][1] && this.data[i][1] == this.data[i][2] && this.data[i][0] != 0) {
                return this.data[i][0]; //cek semua baris
            }
        }

        for (int j = 0; j < 3; j++) {
            if (this.data[0][j] == this.data[1][j] && this.data[1][j] == this.data[2][j] && this.data[0][j] != 0) {
                return this.data[0][j]; // cek kolom
            }
        }

        if (this.data[0][0] == this.data[1][1] && this.data[1][1] == this.data[2][2] && this.data[0][0] != 0) {
            return this.data[0][0]; //cek diagonal
        }

        if (this.data[0][2] == this.data[1][1] && this.data[1][1] == this.data[2][0] && this.data[0][2] != 0) {
            return this.data[0][2]; //cek diagonal
        }

        return 0; //kalau belum ada yang menang dikembalikan lagi, 0
    }

    public boolean gameOver(){
        return winner() != 0 || isFull(); //kalau board ada yang menang, atau seri
    }

    public boolean isFull() {
        for (int i = 0; i < 3; i++) {
            for (int j = 0; j < 3; j++) {
                if (this.data[i][j] == 0) { //cek apakah full
                    return false;
                }
            }
        }
        return true;
    }

    public void resetBoard(){
        for(int i=0; i<3; i++)
            for(int j=0; j<3; j++)
                this.data[i][j] = 0;
    }

    public int getTurn(){
        return this.turn;
    }

    public int[][] getData(){
        return this.data;
    }
}
