<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>TalentKonnect | Notifications</title>
  <style>
    body {
      font-family: 'Inter', sans-serif;
      background-color: #F1FAEE;
      color: #2A2D34;
      padding: 20px;
    }

    .banner {
      background-color: #E76F51;
      color: white;
      padding: 14px 16px;
      border-radius: 12px;
      box-shadow: 2px 2px rgba(0, 0, 0, 0.05);
      position: relative;
      margin-bottom: 20px;
    }

    .dismiss-btn {
      position: absolute;
      top: 8px;
      right: 12px;
      background: none;
      border: none;
      color: white;
      font-size: 20px;
      font-weight: bold;
      cursor: pointer;
    }

    .btn {
      background-color: #1D3557;
      color: white;
      padding: 10px 16px;
      border: none;
      border-radius: 8px;
      font-weight: bold;
      cursor: pointer;
    }

    h2 {
      font-family: 'Montserrat', sans-serif;
      text-transform: uppercase;
    }

    .toggle-container {
      margin-top: 24px;
    }
  </style>
</head>
<body>

  <h2>Notifications</h2>

  <!-- Banner Slot -->
  <div id="bannerContainer"></div>

  <!-- Push Notification Toggle -->
  <div class="toggle-container">
    <p><strong>Push Alerts:</strong></p>
    <button id="toggleBtn" class="btn" onclick="togglePush()">Subscribe</button>
  </div>

  <script>
    // ========================
    // Inline BACKEND SIMULATION
    // ========================

    let pushEnabled = true;

    // Mock database
    const mockDB = {
      notifications: [
        { id: 1, message: "🎉 You earned 10 credits! Join the draw now.", read: false },
        { id: 2, message: "⏰ Reminder: Draw starts in 1 hour!", read: false }
      ],
      logs: []
    };

    // Simulate GET /api/notifications
    function fetchNotifications() {
      return new Promise(resolve => {
        const pending = mockDB.notifications.filter(n => !n.read);
        setTimeout(() => resolve(pending), 300); // fake delay
      });
    }

    // Simulate POST /api/notifications/{id}/ack
    function markAsRead(id) {
      return new Promise(resolve => {
        const notif = mockDB.notifications.find(n => n.id === id);
        if (notif) notif.read = true;
        console.log(`✅ Notification ${id} marked as read`);
        resolve({ success: true });
      });
    }

    // Simulate POST /api/notifications/log-block
    function logBlocked(reason) {
      mockDB.logs.push({
        time: new Date().toISOString(),
        reason: reason
      });
      console.log("🚫 Push Blocked Log:", mockDB.logs);
    }

    // ========================
    // Frontend UI Logic
    // ========================

    window.onload = async () => {
      const notifications = await fetchNotifications();
      if (notifications.length > 0) {
        showBanner(notifications[0].id, notifications[0].message);
      }
    };

    function showBanner(id, message) {
      const container = document.getElementById('bannerContainer');
      container.innerHTML = `
        <div class="banner" id="notif-${id}">
          ${message}
          <button class="dismiss-btn" onclick="dismissNotification(${id})">&times;</button>
        </div>
      `;
    }

    async function dismissNotification(id) {
      await markAsRead(id);
      document.getElementById(`notif-${id}`).remove();
    }

    function togglePush() {
      pushEnabled = !pushEnabled;
      const btn = document.getElementById('toggleBtn');
      btn.textContent = pushEnabled ? 'Unsubscribe' : 'Subscribe';

      if (!pushEnabled) {
        logBlocked("user_blocked");
      }
    }
  </script>
</body>
</html>
