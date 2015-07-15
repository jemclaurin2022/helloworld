# helloworld
import java.sql.CallableStatement;
import java.sql.Connection;
import java.sql.SQLException;
import java.util.Date;

import net.sf.jasperreports.engine.JRDefaultScriptlet;
import net.sf.jasperreports.engine.JRScriptletException;

public class Icdf extends JRDefaultScriptlet {

  public void afterReportInit() throws JRScriptletException {

    // get the current connection from report via parameters
    Connection conn = (Connection) this
      .getParameterValue("REPORT_CONNECTION");

    int userId = 100; //use this.get__ to access from report
    try {
      if (conn != null)
        callOracleStoredProcOUTParameter(conn, userId); // SP call
    } catch (SQLException e) {
      e.printStackTrace();
    }
  }

  private void callOracleStoredProcOUTParameter(Connection conn, int userId)
  throws SQLException {

    CallableStatement callableStatement = null;

    String getDBUSERByUserIdSql = "{call someStoredProcedureName(?,?,?)}";

    try {

      callableStatement = conn.prepareCall(getDBUSERByUserIdSql);

      // setting parameters of the callablestatement
      callableStatement.setInt(1, userId);
      callableStatement.registerOutParameter(2, java.sql.Types.VARCHAR);
      callableStatement.registerOutParameter(3, java.sql.Types.DATE);

      // execute getDBUSERByUserId store procedure
      callableStatement.executeUpdate();

      // get the required OUT parameters from the callablestatement
      String userName = callableStatement.getString(2);
      Date createdDate = callableStatement.getDate(3);

      // --just to check, you can view this on iReport console
      System.out.println("UserName : " + userName + "CreatedDate : " + createdDate);


      // set the values to report variables so that you can use them in
      // the report
      this.setVariableValue("variable_name1", userName);
      this.setVariableValue("variable_name2", createdDate);

    } catch (SQLException e) {
      e.printStackTrace();
    } catch (JRScriptletException e) {
      e.printStackTrace();
    }
  }
}
