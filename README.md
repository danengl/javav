package activity8;

import javax.swing.*;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.sql.*;

public class EmployeeApp {
    private JFrame frame;
    private JTextField searchField, nameField, positionField, salaryField;
    private JButton searchButton, updateButton;
    private Connection conn;

    public EmployeeApp() {
        connectDatabase();
        initializeUI();
    }

    private void connectDatabase() {
        try {
            conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/employeeapp", "root", "password");
        } catch (SQLException e) {
            JOptionPane.showMessageDialog(null, "Database Connection Failed!",
                    "Error", JOptionPane.ERROR_MESSAGE);
            e.printStackTrace();
        }
    }

    private void initializeUI() {
        frame = new JFrame("Employee Management System");
        frame.setSize(400, 250);
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.setLayout(new BorderLayout());
        JPanel panel = new JPanel();
        panel.setLayout(new GridBagLayout());
        GridBagConstraints gbc = new GridBagConstraints();
        gbc.insets = new Insets(5, 5, 5, 5);
        gbc.fill = GridBagConstraints.HORIZONTAL;

        JLabel searchLabel = new JLabel("Search by ID:");
        searchField = new JTextField(15);
        searchButton = new JButton("Search");
        JLabel nameLabel = new JLabel("Name:");
        nameField = new JTextField(15);
        JLabel positionLabel = new JLabel("Position:");
        positionField = new JTextField(15);
        JLabel salaryLabel = new JLabel("Salary:");
        salaryField = new JTextField(15);
        updateButton = new JButton("Update");

        gbc.gridx = 0;
        gbc.gridy = 0;
        panel.add(searchLabel, gbc);
        gbc.gridx = 1;
        panel.add(searchField, gbc);
        gbc.gridx = 2;
        panel.add(searchButton, gbc);

        gbc.gridx = 0;
        gbc.gridy = 1;
        panel.add(nameLabel, gbc);
        gbc.gridx = 1;
        gbc.gridwidth = 2;
        panel.add(nameField, gbc);
        gbc.gridwidth = 1;

        gbc.gridx = 0;
        gbc.gridy = 2;
        panel.add(positionLabel, gbc);
        gbc.gridx = 1;
        gbc.gridwidth = 2;
        panel.add(positionField, gbc);
        gbc.gridwidth = 1;

        gbc.gridx = 0;
        gbc.gridy = 3;
        panel.add(salaryLabel, gbc);
        gbc.gridx = 1;
        gbc.gridwidth = 2;
        panel.add(salaryField, gbc);
        gbc.gridwidth = 1;

        gbc.gridx = 1;
        gbc.gridy = 4;
        panel.add(updateButton, gbc);

        frame.add(panel, BorderLayout.CENTER);
        frame.setVisible(true);

        searchButton.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                searchEmployee();
            }
        });

        updateButton.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                updateEmployee();
            }
        });
    }

    private void searchEmployee() {
        try {
            String id = searchField.getText();
            PreparedStatement stmt = conn.prepareStatement("SELECT * FROM employee WHERE id = ?");
            stmt.setString(1, id);
            ResultSet rs = stmt.executeQuery();
            if (rs.next()) {
                nameField.setText(rs.getString("name"));
                positionField.setText(rs.getString("position"));
                salaryField.setText(rs.getString("salary"));
            } else {
                JOptionPane.showMessageDialog(frame, "Employee not found!", "Error", JOptionPane.ERROR_MESSAGE);
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    private void updateEmployee() {
        try {
            String id = searchField.getText();
            String name = nameField.getText();
            String position = positionField.getText();
            String salary = salaryField.getText();

            // Ensure the ID field is not empty
            if (id.isEmpty() || name.isEmpty() || position.isEmpty() || salary.isEmpty()) {
                JOptionPane.showMessageDialog(frame, "Please fill in all fields.", "Error", JOptionPane.ERROR_MESSAGE);
                return;
            }

            // Prepare the update statement
            PreparedStatement stmt = conn.prepareStatement("UPDATE employee SET name = ?, position = ?, salary = ? WHERE id = ?");
            stmt.setString(1, name);
            stmt.setString(2, position);
            stmt.setString(3, salary);
            stmt.setString(4, id);

            // Execute the update
            int rowsUpdated = stmt.executeUpdate();

            if (rowsUpdated > 0) {
                JOptionPane.showMessageDialog(frame, "Employee updated successfully!", "Success", JOptionPane.INFORMATION_MESSAGE);
                clearFields();
            } else {
                JOptionPane.showMessageDialog(frame, "Update failed!", "Error", JOptionPane.ERROR_MESSAGE);
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    private void clearFields() {
        searchField.setText("");
        nameField.setText("");
        positionField.setText("");
        salaryField.setText("");
    }

    public static void main(String[] args) {
        new EmployeeApp();
    }
}
