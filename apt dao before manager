package appointment;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

import Package.Package;

import util.ConnectionManager;

public class AppointmentDAO {
	private static Connection connection = null;


	public static byte[] getPackageImage(int id) throws SQLException {
		// TODO Auto-generated method stub
		byte[] image = null;
		String query = "SELECT packagePic FROM package WHERE packageID=?";

		try (Connection conn = ConnectionManager.getConnection(); PreparedStatement ps = conn.prepareStatement(query)) {
			ps.setInt(1, id);
			ResultSet rs = ps.executeQuery();

			if (rs.next()) {
				image = rs.getBytes("packagePic");
			}
		}
		return image;
	}

	public static void bookAppointment(appointment appt) throws SQLException {
		// TODO Auto-generated method stub
		String query = "INSERT INTO appointment(cusID, staffID, packageID, apptDate,apptTime, notiStatus) VALUES (?,?,?,?,?,?)";
		connection = ConnectionManager.getConnection();
		PreparedStatement ps = connection.prepareStatement(query);
		try {
			ps.setInt(1, appt.getCustomerID());
			ps.setInt(2, appt.getStaffID());
			ps.setInt(3, appt.getPackageID());
			ps.setDate(4, appt.getApptDate());
			ps.setTimestamp(5, appt.getApptTime());
			ps.setString(6, "PENDING");

			ps.executeUpdate();
			ps.close();
		} catch (SQLException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}

	}

	public static appointment getAppointmentById(int appointmentID) {
		appointment apt = null;
		String sql = "SELECT a.*, c.custName, p.packageName, p.packagePrice, s.name AS staffName "
				+ "FROM appointment a " + "JOIN customer c ON a.cusID = c.cusID "
				+ "JOIN package p ON a.packageID = p.packageID " + "JOIN staff s ON a.staffID = s.staffID "
				+ "WHERE a.appointmentID = ?";

		try (Connection conn = ConnectionManager.getConnection(); PreparedStatement ps = conn.prepareStatement(sql)) {

			ps.setInt(1, appointmentID);

			try (ResultSet rs = ps.executeQuery()) {
				if (rs.next()) {
					apt = new appointment();
					apt.setAppointmentID(rs.getInt("appointmentID"));
					apt.setApptDate(rs.getDate("apptDate"));
					apt.setApptTime(rs.getTimestamp("apptTime"));

					// Set additional fields for viewapt.jsp
					apt.setCustomerName(rs.getString("custName"));
					apt.setPackageName(rs.getString("packageName"));
					apt.setPackagePrice(rs.getDouble("packagePrice"));
					apt.setPharmacistName(rs.getString("staffName"));
				}
			}

		} catch (SQLException e) {
			e.printStackTrace();
		}

		return apt;
	}

	public static List<appointment> getAllAppointmentsByCustomerId(int cusID) {

	    List<appointment> appointments = new ArrayList<>();

	    String sql = "SELECT a.*, p.packageName, c.custName "
	            + "FROM appointment a "
	            + "JOIN package p ON a.packageID = p.packageID "
	            + "JOIN customer c ON a.cusID = c.cusID "
	            + "WHERE a.cusID = ? "
	            + "ORDER BY a.apptDate DESC, a.apptTime DESC";

	    try (Connection conn = ConnectionManager.getConnection();
	         PreparedStatement ps = conn.prepareStatement(sql)) {

	        ps.setInt(1, cusID);

	        ResultSet rs = ps.executeQuery();

	        while (rs.next()) {

	            appointment apt = new appointment();

	            apt.setAppointmentID(rs.getInt("appointmentID"));
	            apt.setApptDate(rs.getDate("apptDate"));
	            apt.setApptTime(rs.getTimestamp("apptTime"));
	            apt.setPackageName(rs.getString("packageName"));
	            apt.setCustomerName(rs.getString("custName"));

	            appointments.add(apt);
	        }

	        rs.close();
	        ps.close();

	    } catch (SQLException e) {
	        e.printStackTrace();
	    }

	    return appointments;
	}

	public static void cancelAppointment(int appointmentID) throws SQLException {
		String sql = "DELETE FROM appointment WHERE appointmentID = ?";

		try (Connection conn = ConnectionManager.getConnection(); PreparedStatement ps = conn.prepareStatement(sql)) {

			ps.setInt(1, appointmentID);
			ps.executeUpdate();
			ps.close();
		} catch (SQLException e) {
			e.printStackTrace();
		}
	}

	// Notification Status
	public static void updateNotificationStatus(int appointmentID, String status) throws SQLException {
		String sql = "UPDATE appointment SET notiStatus = ? WHERE appointmentID = ?";
		try (Connection conn = ConnectionManager.getConnection(); PreparedStatement ps = conn.prepareStatement(sql)) {
			ps.setString(1, status);
			ps.setInt(2, appointmentID);
			ps.executeUpdate();
		}
	}

	// Cari list appointment untuk send notification
	public static List<appointment> getAppointmentsForReminder(int interval, String unit) {
		List<appointment> appointments = new ArrayList<>();
		String sql = "";

		// Pilih query ikut unit (DAY atau HOUR)
		if ("DAY".equalsIgnoreCase(unit)) {
			sql = "SELECT a.*, c.custEmail " + "FROM appointment a " + "JOIN customer c ON a.cusID = c.cusID "
					+ "WHERE TRUNC(a.apptDate) - TRUNC(SYSDATE) = ? " + "AND a.notiStatus = 'PENDING'";
		} else if ("HOUR".equalsIgnoreCase(unit)) {
			sql = "SELECT a.*, c.custEmail " + "FROM appointment a " + "JOIN customer c ON a.cusID = c.cusID "
					+ "WHERE ROUND((a.apptTime - SYSDATE) * 24) = ? " + "AND a.notiStatus = 'PENDING'";
		}

		try (Connection conn = ConnectionManager.getConnection(); PreparedStatement ps = conn.prepareStatement(sql)) {

			ps.setInt(1, interval);
			ResultSet rs = ps.executeQuery();

			while (rs.next()) {
				appointment apt = new appointment();
				apt.setAppointmentID(rs.getInt("appointmentID"));
				apt.setApptDate(rs.getDate("apptDate"));
				apt.setApptTime(rs.getTimestamp("apptTime"));
				apt.setCustomerEmail(rs.getString("custEmail"));
				appointments.add(apt);
			}
		} catch (SQLException e) {
			e.printStackTrace();
		}
		return appointments;
	}

	public static List<appointment> getAllAppointmentsByStaffId(Integer staffID) {
		// TODO Auto-generated method stub
		List<appointment> appointments = new ArrayList<>();
		String sql = "SELECT a.*, p.packageName,c.custName FROM appointment a "
				+ "JOIN package p ON a.packageID = p.packageID " + "JOIN customer c ON a.cusID = c.cusID "
				+ "WHERE a.staffID = ? ORDER BY a.apptDate DESC";

		try (Connection conn = ConnectionManager.getConnection(); PreparedStatement ps = conn.prepareStatement(sql)) {
			ps.setInt(1, staffID);
			ResultSet rs = ps.executeQuery();
			while (rs.next()) {
				appointment apt = new appointment();
				apt.setAppointmentID(rs.getInt("appointmentID"));
				apt.setApptDate(rs.getDate("apptDate"));
				apt.setApptTime(rs.getTimestamp("apptTime"));
				apt.setPackageName(rs.getString("packageName"));
				apt.setCustomerName(rs.getString("custName"));
				appointments.add(apt);
			}
			ps.close();
		} catch (SQLException e) {
			e.printStackTrace();
		}
		return appointments;
	}

	public static Map<String, List<String>> getAllBookedSlots() {
	    Map<String, List<String>> map = new HashMap<>();
	    String sql = "SELECT apptDate, DATE_FORMAT(apptTime, '%H:%i') FROM appointment";
	    
	    try (Connection conn = ConnectionManager.getConnection();
	         PreparedStatement ps = conn.prepareStatement(sql);
	         ResultSet rs = ps.executeQuery()) {
	        
	        while (rs.next()) {
	            String date = rs.getString(1); // e.g. "20/01/2026"
	            String time = rs.getString(2); // e.g. "08:00"
	            map.computeIfAbsent(date, k -> new ArrayList<>()).add(time);
	        }
	    } catch (Exception e) { e.printStackTrace(); }
	    return map;
	}

	public static List<Package> getAllPackages() {

	    List<Package> packages = new ArrayList<>();

	    String query = "SELECT * FROM JuzCare.package";

	    try (Connection connection = ConnectionManager.getConnection();
	         PreparedStatement ps = connection.prepareStatement(query);
	         ResultSet rs = ps.executeQuery()) {

	        while (rs.next()) {

	            Package p = new Package();

	            p.setPackageID(rs.getInt("packageID"));
	            p.setPackagePic(rs.getBinaryStream("packagePic"));
	            p.setPackageName(rs.getString("packageName"));
	            p.setPackagePrice(rs.getDouble("packagePrice"));
	            p.setIsExist(rs.getString("isExist"));

	            packages.add(p);
	        }

	    } catch (SQLException e) {
	        e.printStackTrace();
	    }

	    return packages;
	}

	public static List<String> getBookedTimes(String date)
	        throws SQLException {

	    List<String> times = new ArrayList<>();

	    String sql =
	        "SELECT TO_CHAR(apptTime, 'HH24:MI') AS bookedTime " +
	        "FROM appointment " +
	        "WHERE apptDate = ?";

	    try (Connection conn = ConnectionManager.getConnection();
	         PreparedStatement ps = conn.prepareStatement(sql)) {

	        ps.setDate(1, java.sql.Date.valueOf(date));

	        ResultSet rs = ps.executeQuery();

	        while (rs.next()) {
	            times.add(rs.getString("bookedTime"));
	        }
	    }
	    System.out.println("Selected date = " + date);
	    System.out.println("Booked times = " + times);

	    return times;
	}
	
	public static List<appointment> getAllAppointments() {

	    List<appointment> appointments = new ArrayList<>();

	    String sql =
	        "SELECT a.*, p.packageName, c.custName " +
	        "FROM appointment a " +
	        "JOIN package p ON a.packageID = p.packageID " +
	        "JOIN customer c ON a.cusID = c.cusID " +
	        "ORDER BY a.apptDate DESC";

	    try (Connection conn = ConnectionManager.getConnection();
	         PreparedStatement ps = conn.prepareStatement(sql);
	         ResultSet rs = ps.executeQuery()) {

	        while (rs.next()) {
	            appointment apt = new appointment();
	            apt.setAppointmentID(rs.getInt("appointmentID"));
	            apt.setApptDate(rs.getDate("apptDate"));
	            apt.setApptTime(rs.getTimestamp("apptTime"));
	            apt.setPackageName(rs.getString("packageName"));
	            apt.setCustomerName(rs.getString("custName"));

	            appointments.add(apt);
	        }

	    } catch (SQLException e) {
	        e.printStackTrace();
	    }

	    return appointments;
	}
}
