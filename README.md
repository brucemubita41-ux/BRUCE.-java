import javax.swing.*;
import javax.swing.table.DefaultTableModel;
import java.awt.*;
import java.awt.event.*;
import java.util.ArrayList;

public class CustomerManagerApp extends JFrame {
    // UI fields for Requirement 1 & 3
    private JTextField txtName = new JTextField(15);
    private JComboBox<String> cbProv = new JComboBox<>(new String[]{"-- Select --", "Ontario", "Quebec", "Alberta", "BC"});
    private DefaultTableModel model = new DefaultTableModel(new String[]{"Name", "Province"}, 0);
    private JTable table = new JTable(model);
    
    // Requirement 2: Structural Data List
    private ArrayList<Customer> customerList = new ArrayList<>();
    public static class Customer {
        String name, prov;
        Customer(String n, String p) { name = n; prov = p; }
    }

    public CustomerManagerApp() {
        setTitle("Customer Manager Lab");
        setSize(450, 350);
        setDefaultCloseOperation(EXIT_ON_CLOSE);
        setLocationRelativeTo(null);

        // Layout the input Form Panel
        JPanel form = new JPanel(new GridLayout(3, 2, 5, 5));
        form.add(new JLabel(" Name:")); form.add(txtName);
        form.add(new JLabel(" Province:")); form.add(cbProv);
        JButton btnAdd = new JButton("Add"); JButton btnDel = new JButton("Delete");
        form.add(btnAdd); form.add(btnDel);

        add(form, BorderLayout.NORTH);
        add(new JScrollPane(table), BorderLayout.CENTER);

        // Click actions
        btnAdd.addActionListener(e -> addCustomer());
        btnDel.addActionListener(e -> deleteCustomer());

        // Requirement 6: Keyboard Access (Enter to Add, Delete to Remove)
        KeyAdapter enterKey = new KeyAdapter() {
            public void keyPressed(KeyEvent e) { if(e.getKeyCode() == KeyEvent.VK_ENTER) addCustomer(); }
        };
        txtName.addKeyListener(enterKey);
        cbProv.addKeyListener(enterKey);
        table.addKeyListener(new KeyAdapter() {
            public void keyPressed(KeyEvent e) { if(e.getKeyCode() == KeyEvent.VK_DELETE) deleteCustomer(); }
        });
    }

    // Requirement 4 & 6: Validate inputs, then save data and update grid UI
    private void addCustomer() {
        String name = txtName.getText().trim();
        String prov = (String) cbProv.getSelectedItem();

        if (name.isEmpty() || cbProv.getSelectedIndex() == 0) {
            JOptionPane.showMessageDialog(this, "Validation Error: Fields cannot be blank!", "Error", JOptionPane.ERROR_MESSAGE);
            return;
        }

        customerList.add(new Customer(name, prov));
        model.addRow(new Object[]{name, prov}); 

        txtName.setText(""); cbProv.setSelectedIndex(0); txtName.requestFocus();
    }

    // Requirement 5: Confirm deletion window before removing records
    private void deleteCustomer() {
        int row = table.getSelectedRow();
        if (row == -1) {
            JOptionPane.showMessageDialog(this, "Please select a row first.", "Warning", JOptionPane.WARNING_MESSAGE);
            return;
        }

        if (JOptionPane.showConfirmDialog(this, "Confirm delete?", "Confirm", JOptionPane.YES_NO_OPTION) == JOptionPane.YES_OPTION) {
            customerList.remove(row);
            model.removeRow(row);
        }
    }

    public static void main(String[] args) {
        SwingUtilities.invokeLater(() -> new CustomerManagerApp().setVisible(true));
    }
}
