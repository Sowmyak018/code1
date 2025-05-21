# code1
//Creating Table in Oracle DB
CREATE TABLE messages (
  id NUMBER PRIMARY KEY,
  message_date DATE,
  message_time VARCHAR2(10),
  message_text VARCHAR2(4000)
);

const express = require('express');
const bodyParser = require('body-parser');
const oracledb = require('oracledb');

const app = express();
const PORT = 3000;

app.use(bodyParser.json());

// Oracle DB connection
const dbConfig = {
  user: 'system',
  password: '1234',
  connectString: 'localhost/XEPDB1' 
};

app.post('/api/messages', async (req, res) => {
  const { date, time, message } = req.body;

  let connection;
  }
  try {
    connection = await oracledb.getConnection(dbConfig);

    const result = await connection.execute(
      'INSERT INTO messages (message_date, message_time, message_text) 
       VALUES (TO_DATE(:date, 'YYYY-MM-DD'), :time, :message)',
      [date, time, message],
      { autoCommit: true }
    );

  } catch (err) {
    console.error('DB Error:', err);
    res.status(500).json({ error: 'Database error', details: err.message });
  } finally {
    if (connection) {
      try {
        await connection.close();
      } catch (err) {
        console.error('Error closing connection', err);
      }
    }
  }
});

app.listen(PORT, () => {
  console.log('Server running on http://localhost:${PORT}');
});
