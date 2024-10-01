# Konsep Pemrograman Berorientasi Objek - Simulasi UTS

---

## Langkah - langkah : 
### 1. Membuat Database baru pada postgreSQL
### 2. Membuat tabel beserta atributnya pada postgreSQL
### 3. Membuat project baru pada Netbeans
### 4. Mengoneksikan postgreSQL dengan java menggunakan JDBC
   <pre>
    Connection conn;
    Statement stmt;
    PreparedStatement pstmt = null;

    String driver = "org.postgresql.Driver";
    String koneksi = "jdbc:postgresql://localhost:5432/Simulasi_UTS";
    String user = "postgres";
    String password = "Laura12345";
    InputStreamReader inputStreamReader = new InputStreamReader(System.in);
    BufferedReader input = new BufferedReader(inputStreamReader);
   </pre>

### 5. Membuat User Interface dengan Java Swing
  Menggunakan beberapa fitur pada Java Swing :
  - Label yang akan digunakan untuk membuat teks (ISBN, Judul Buku, Tahun Terbit, Penerbit)
  - Text Field untuk membuat field data (tfISBN, tfJudul, tfTahun, tfPenerbit)
  - Button untuk membuat tombol pengoperasian CRUD (btnTambah, btnUpdate, btnHapus)
  - Table untuk membuat tabel yang digunakan untuk menampilkan data (Tabel) 
  
### 6. Penggunaan JTable
   <pre>
     public void showTable() {
     try (Connection conn = DriverManager.getConnection(koneksi, user, password); Statement stmt = conn.createStatement(); ResultSet rs = stmt.executeQuery("SELECT * FROM Buku ORDER BY isbn")) {

            // Dapatkan model tabel yang ada
            DefaultTableModel model = (DefaultTableModel) Tabel.getModel();

            // Kosongkan data tabel terlebih dahulu
            model.setRowCount(0);

            // Tambahkan data dari ResultSet ke model tabel
            while (rs.next()) {
                Object[] rowData = {rs.getString(1), rs.getString(2), rs.getString(3), rs.getString(4)};
                model.addRow(rowData);
            }
        } catch (SQLException ex) {
            // Tangani eksepsi
            System.err.println("Terjadi kesalahan: " + ex.getMessage());
        }
      }
   </pre>

### 7. Penggunaan btnTambah
   Pada Button Tambah (btnTambah), berfungsi untuk menambah isi data pada database.  
   <pre>
   private void btnTambahActionPerformed(java.awt.event.ActionEvent evt) {//GEN-FIRST:event_btnTambahActionPerformed
        // TODO add your handling code here:
        if (tfISBN.getText().equals("")) {
            JOptionPane.showMessageDialog(null, "Isi semua data");
        } else if (tfJudul.getText().equals("")) {
            JOptionPane.showMessageDialog(null, "Isi semua data");
        } else if (tfTahun.getText().equals("")) {
            JOptionPane.showMessageDialog(null, "Isi semua data");
        } else if (tfPenerbit.getText().equals("")) {
            JOptionPane.showMessageDialog(null, "Isi semua data");
        } else {
            try {
                Class.forName(driver);
                conn = DriverManager.getConnection(koneksi, user, password);
                conn.setAutoCommit(false);

                String sql = "INSERT INTO Buku VALUES(?,?,?,?)";
                pstmt = conn.prepareStatement(sql);

                String isbn, judul_buku, tahun_terbit, penerbit;
                isbn = tfISBN.getText();
                judul_buku = tfJudul.getText();
                tahun_terbit = tfTahun.getText();
                penerbit = tfPenerbit.getText();

                pstmt.setString(1, isbn);
                pstmt.setString(2, judul_buku);
                pstmt.setString(3, tahun_terbit);
                pstmt.setString(4, penerbit);

                pstmt.executeUpdate();
                conn.commit();
                pstmt.close();
                conn.close();

                JOptionPane.showMessageDialog(null, "Sukses diinput");
            } catch (ClassNotFoundException | SQLException ex) {
                JOptionPane.showMessageDialog(null, "Terjadi kesalahan saat pengisian");
            }
        }
        showTable();                   
    }
   </pre>
   
### 8. Penggunaan btnUpdate
  Pada Button Update (btnUpdate), berfungsi untuk merubah isi data yang ada dalam database. Fungsi ini akan dijalankan ketika data pada Tabel (JTable) dipilih. 
   <pre>
   private void btnUpdateActionPerformed(java.awt.event.ActionEvent evt) {//GEN-FIRST:event_btnUpdateActionPerformed
        // TODO add your handling code here:
   String isbn, judul_buku, tahun_terbit, penerbit;
        if (tfISBN.getText().equals("")) {
            JOptionPane.showMessageDialog(null, "Isi semua data");
        } else if (tfJudul.getText().equals("")) {
            JOptionPane.showMessageDialog(null, "Isi semua data");
        } else if (tfTahun.getText().equals("")) {
            JOptionPane.showMessageDialog(null, "Isi semua data");
        } else if (tfPenerbit.getText().equals("")) {
            JOptionPane.showMessageDialog(null, "Isi semua data");
        } else {
            try {
                Class.forName(driver);
                String sql = "UPDATE Buku SET judul_buku = ?, tahun_terbit = ?, penerbit = ? WHERE isbn = ?";
                conn = DriverManager.getConnection(koneksi, user, password);
                pstmt = conn.prepareStatement(sql);

                isbn = tfISBN.getText();
                judul_buku = tfJudul.getText();
                tahun_terbit = tfTahun.getText();
                penerbit = tfPenerbit.getText();

                pstmt.setString(1, judul_buku);
                pstmt.setString(2, tahun_terbit);
                pstmt.setString(3, penerbit);
                pstmt.setString(4, isbn);

                int rowsAffected = pstmt.executeUpdate();
                if (rowsAffected > 0) {
                    JOptionPane.showMessageDialog(null, "Data berhasil diupdate");
                    pstmt.close();
                    conn.close();
                    bersih();
                } else {
                    JOptionPane.showMessageDialog(null, "Data tidak ditemukan");
                }
            } catch (ClassNotFoundException | SQLException ex) {
                Logger.getLogger(Buku.class.getName()).log(Level.SEVERE, null, ex);
            }
        }
        showTable();
    }
   </pre>

### 9. Penggunaan btnHapus
Pada Button Hapus (btnHapus), berfungsi untuk menghapus isi data yang ada dalam database. Fungsi ini akan dijalankan ketika data pada Tabel (JTable) dipilih. 
<pre>
  private void btnHapusActionPerformed(java.awt.event.ActionEvent evt) {//GEN-FIRST:event_btnHapusActionPerformed
        // TODO add your handling code here:
        String isbn;
        isbn = tfISBN.getText();

        try {
            Class.forName(driver);
            conn = DriverManager.getConnection(koneksi, user, password);

            int jawab = JOptionPane.showConfirmDialog(null, "Silakan Konfirmasi?");
            switch (jawab) {
                case JOptionPane.YES_OPTION:
                    String deleteSql = "DELETE FROM Buku WHERE isbn= ?";
                    pstmt = conn.prepareStatement(deleteSql);
                    pstmt.setString(1, isbn);
                    pstmt.executeUpdate();
                    pstmt.close();
                    conn.close();
                    bersih();
                    break;
                case JOptionPane.NO_OPTION:
                    JOptionPane.showMessageDialog(this, "Kamu menjawab tidak");
                    break;
            }
        } catch (ClassNotFoundException | SQLException ex) {
            JOptionPane.showConfirmDialog(null, "Cek Lagi!!!");
        }
        showTable();
    }
</pre>
