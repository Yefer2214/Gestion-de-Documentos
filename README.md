<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>Sistema de Control y Registro de Documentos</title>
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
  <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/flatpickr/dist/flatpickr.min.css">
  <script src="https://cdn.jsdelivr.net/npm/flatpickr"></script>
  <!-- SheetJS for Excel export -->
  <script src="https://cdn.sheetjs.com/xlsx-0.20.1/package/dist/xlsx.full.min.js"></script>
  <!-- Google Fonts: Inter is closer to Windows 11 Segoe UI -->
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap" rel="stylesheet">
  <!-- SweetAlert2 for better notifications -->
  <script src="https://cdn.jsdelivr.net/npm/sweetalert2@11"></script>
  <!-- Chart.js for statistics -->
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
  <!-- Tesseract.js for OCR (Auto-fill) -->
  <script src="https://cdn.jsdelivr.net/npm/tesseract.js@5/dist/tesseract.min.js"></script>
  <style>
    :root {
      --primary-color: #0078d4; /* Azul Windows 11 */
      --primary-dark: #005a9e;
      --secondary-color: #64748b;
      --accent-color: #7f8c8d;
      --success-color: #107c10;
      --warning-color: #d35400;
      --danger-color: #ef4444;
      --info-color: #3b82f6;
      --body-bg: #f8fafc;
      --card-bg: #ffffff;
      --text-primary: #1e293b;
      --text-secondary: #64748b;
      --border-radius: 12px;
      --shadow-sm: 0 2px 8px rgba(0,0,0,0.04);
      --shadow-md: 0 8px 32px rgba(0,0,0,0.08);
      --shadow-lg: 0 16px 48px rgba(0,0,0,0.12);
      --gradient-primary: linear-gradient(135deg, #0078d4 0%, #005a9e 100%);
      --gradient-header: linear-gradient(135deg, #2c3e50 0%, #2980b9 100%);
      --gradient-login: linear-gradient(135deg, #2c3e50 0%, #34495e 100%);
    }

    body {
      font-family: 'Inter', -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
      background-color: var(--body-bg);
      color: #000000; /* Texto más oscuro para legibilidad */
      line-height: 1.6;
      font-size: 0.9rem;
    }

    /* Sidebar Layout */
    .app-content.show {
      display: block;
    }

    .sidebar {
      width: 280px;
      height: 100vh;
      position: fixed;
      top: 0;
      left: 0;
      background: rgba(249, 249, 249, 0.85);
      -webkit-backdrop-filter: blur(40px) saturate(180%);
      backdrop-filter: blur(40px) saturate(180%);
      -webkit-backdrop-filter: blur(40px) saturate(180%);
      border-right: 1px solid rgba(0,0,0,0.1);
      padding: 1.5rem;
      display: flex;
      flex-direction: column;
      z-index: 1000;
      overflow-y: auto;
    }

    .main-content {
      flex: 1;
      margin-left: 280px;
      min-height: 100vh;
      display: flex;
      flex-direction: column;
    }

    .sidebar-brand {
      display: flex;
      align-items: center;
      color: #000;
      font-weight: 700;
      font-size: 1.2rem;
      text-decoration: none;
      margin-bottom: 2rem;
      padding-left: 0.5rem;
    }

    .sidebar-brand i {
      font-size: 1.8rem;
      margin-right: 0.8rem;
    }

    .sidebar-brand img {
      height: 40px; /* Ajusta el tamaño del logo en el menú */
      margin-right: 0.8rem;
    }

    .sidebar .nav-link {
      display: flex;
      align-items: center;
      color: var(--text-secondary);
      padding: 0.8rem 1rem;
      border-radius: 12px;
      margin-bottom: 0.5rem;
      transition: all 0.3s ease;
      text-decoration: none;
      font-weight: 500;
    }

    .sidebar .nav-link:hover, .sidebar .nav-link.active {
      background-color: rgba(44, 62, 80, 0.08);
      color: var(--primary-color);
    }

    .sidebar .nav-link i {
      width: 24px;
      margin-right: 10px;
      text-align: center;
    }
    
    .sidebar .nav-link.text-danger {
      color: var(--danger-color);
      margin-top: auto;
    }
    
    .sidebar .nav-link.text-danger:hover {
      background-color: rgba(239, 68, 68, 0.1);
    }

    .app-header {
      background: var(--gradient-header);
      color: white;
      padding: 1.5rem 0;
      margin-bottom: 2rem;
      border-radius: 0 0 24px 24px;
      box-shadow: var(--shadow-lg);
      text-align: center;
      position: relative;
      overflow: hidden;
    }
    
    .app-header::before {
      content: '';
      position: absolute;
      top: 0; left: 0; right: 0; bottom: 0;
      background: radial-gradient(circle at top right, rgba(255,255,255,0.1), transparent);
    }

    .app-header h1 {
      font-weight: 700;
      letter-spacing: -1px;
      position: relative;
    }

    .card {
      border: none;
      border-radius: 12px;
      border: 1px solid rgba(255, 255, 255, 0.4);
      background: rgba(255, 255, 255, 0.75);
      -webkit-backdrop-filter: blur(20px);
      backdrop-filter: blur(20px);
      box-shadow: var(--shadow-md);
      margin-bottom: 2rem;
      transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
    }

    .card:hover {
      box-shadow: var(--shadow-lg);
      transform: translateY(-2px);
    }

    .card-header {
      background: transparent;
      border-bottom: 1px solid #f1f5f9;
      font-weight: 600;
      color: var(--primary-color);
      padding: 1.25rem 1.5rem;
      border-radius: var(--border-radius) var(--border-radius) 0 0 !important;
    }
    
    .card-body {
      padding: 1.5rem;
    }

    .btn {
      border-radius: 10px;
      padding: 0.6rem 1.2rem;
      font-weight: 500;
      transition: all 0.3s ease;
    }

    .btn-primary {
      background: var(--gradient-primary);
      border: none;
      box-shadow: 0 4px 6px rgba(44, 62, 80, 0.25);
      box-shadow: 0 4px 6px rgba(99, 102, 241, 0.2);
      color: white;
    }

    .btn-primary:hover, .btn-primary:focus {
      transform: translateY(-2px);
      box-shadow: 0 8px 15px rgba(44, 62, 80, 0.35);
      background: linear-gradient(135deg, #1a252f 0%, #2c3e50 100%);
    }

    .form-label {
      font-weight: 500;
      color: var(--text-secondary);
      font-size: 0.9rem;
      margin-bottom: 0.5rem;
    }

    .required:after {
      content: "*";
      color: var(--danger-color);
      margin-left: 4px;
    }

    .form-control, .form-select {
      border-radius: 10px;
      border: 1px solid #e2e8f0;
      padding: 0.75rem 1rem;
      transition: all 0.3s;
      font-size: 0.95rem;
    }

    .form-control:focus, .form-select:focus {
      border-color: var(--primary-color);
      box-shadow: 0 0 0 4px rgba(44, 62, 80, 0.1);
      outline: none;
    }

    .preview-container {
      max-height: 220px;
      overflow: hidden;
      border: 2px dashed #e2e8f0;
      border-radius: 12px;
      display: flex;
      align-items: center;
      justify-content: center;
      background: #f8fafc;
      padding: 10px;
    }

    .preview-container img {
      max-width: 100%;
      max-height: 200px;
    }

    .preview-pdf {
      display: flex;
      align-items: center;
      justify-content: center;
      padding: 20px;
      background-color: #fff;
    }

    .preview-pdf i {
      font-size: 48px;
      color: var(--danger-color);
    }

    .table-container {
      border-radius: var(--border-radius);
      box-shadow: var(--shadow-sm);
      background: #fff;
      overflow: hidden;
      border: 1px solid #e2e8f0;
      overflow-x: auto;
    }

    .search-form {
      margin-bottom: 1.2rem;
    }

    .table thead th {
      background-color: #f8fafc;
      color: var(--text-secondary);
      font-weight: 600;
      text-transform: uppercase;
      font-size: 0.75rem;
      letter-spacing: 0.05em;
      border-bottom: 2px solid #e2e8f0;
      padding: 1rem;
    }

    .table tbody td {
      padding: 1rem;
      vertical-align: middle;
      color: var(--text-primary);
      border-bottom: 1px solid #f1f5f9;
    }

    .dropzone {
      border: 2px dashed #cbd5e1;
      border-radius: 16px;
      background: #f8fafc;
      padding: 3rem 2rem;
      text-align: center;
      transition: all 0.3s ease;
      cursor: pointer;
    }

    .dropzone:hover, .dropzone.dragover {
      border-color: var(--primary-color);
      background: #eff6ff;
      transform: translateY(-3px);
    }

    .dropzone i {
      font-size: 48px;
      color: var(--primary-color);
      margin-bottom: 1rem;
      opacity: 0.8;
    }

    .loader {
      display: none;
      text-align: center;
      padding: 20px;
    }

    .loader i {
      font-size: 32px;
      color: var(--primary-color);
    }

    .login-screen {
      min-height: 100vh;
      display: flex;
      background: url('https://images.unsplash.com/photo-1633513364239-6b2c659ba9bf?q=80&w=2070&auto=format&fit=crop') no-repeat center center;
      background: url('https://images.unsplash.com/photo-1620641788421-7a1c342ea42e?q=80&w=1974&auto=format&fit=crop') no-repeat center center fixed;
      background-size: cover;
      position: relative;
    }

    .login-card {
      background: rgba(255, 255, 255, 0.8) !important;
      -webkit-backdrop-filter: blur(50px) saturate(200%);
      backdrop-filter: blur(50px) saturate(200%);
      -webkit-backdrop-filter: blur(50px) saturate(200%);
      background: rgba(255, 255, 255, 0.7) !important;
      -webkit-backdrop-filter: blur(40px) saturate(180%);
      backdrop-filter: blur(40px) saturate(180%);
      border-radius: 16px;
      border: 1px solid rgba(255, 255, 255, 0.6);
      box-shadow: 0 20px 40px rgba(0, 0, 0, 0.3);
      padding: 3rem;
      width: 100%;
      max-width: 420px;
      margin: auto;
    }

    .login-logo {
      text-align: center;
      margin-bottom: 2rem;
    }

    .login-logo i {
      font-size: 3.5rem;
      color: var(--primary-color);
      margin-bottom: 1rem;
    }

    .login-logo img {
      max-height: 120px; /* Ajusta el tamaño del logo en el login */
      margin-bottom: 1rem;
    }

    .login-logo h2 {
      color: #000;
      font-weight: 700;
      margin-bottom: 0.5rem;
      letter-spacing: -0.5px;
    }

    .login-logo p {
      color: #444;
      font-size: 0.95rem;
    }

    .login-form .input-group-text {
      background: transparent;
      border: 1px solid rgba(0,0,0,0.1);
      border-right: none;
      border-radius: 10px 0 0 10px;
      color: var(--text-secondary);
    }

    .login-form .input-group .form-control {
      border-left: none;
      border-radius: 0 10px 10px 0;
    }

    .login-form .input-group:focus-within .input-group-text {
      border-color: var(--primary-color);
      color: var(--primary-color);
    }

    .btn-login {
      background: var(--gradient-primary);
      border: none;
      padding: 0.9rem;
      border-radius: 10px;
      font-weight: 600;
      font-size: 1rem;
      transition: transform 0.2s ease, box-shadow 0.2s ease;
      color: white;
    }

    .btn-login:hover {
      transform: translateY(-2px);
      box-shadow: 0 10px 25px rgba(44, 62, 80, 0.4);
    }

    .login-error {
      background: rgba(239, 68, 68, 0.1);
      border: 1px solid var(--danger-color);
      color: var(--danger-color);
      padding: 0.75rem 1rem;
      border-radius: 8px;
      font-size: 0.9rem;
      display: none;
    }

    .login-error.show {
      display: block;
    }

    .app-content {
      display: none;
    }

    .app-content.show {
      display: block;
    }
    
    /* Stats Cards Colors */
    .stats-card-icon {
        width: 60px;
        height: 60px;
        border-radius: 50%;
        display: flex;
        align-items: center;
        justify-content: center;
        font-size: 1.5rem;
        margin: 0 auto 1rem;
    }

    /* IxD Improvements: Animations & Scrollbars */
    @keyframes fadeInScale {
      from { opacity: 0; transform: scale(0.99) translateY(10px); }
      to { opacity: 1; transform: scale(1) translateY(0); }
    }
    
    .fade-in {
      animation: fadeInScale 0.4s cubic-bezier(0.25, 0.46, 0.45, 0.94) both;
    }

    ::-webkit-scrollbar {
      width: 8px;
      height: 8px;
    }
    ::-webkit-scrollbar-track {
      background: rgba(0,0,0,0.05); 
    }
    ::-webkit-scrollbar-thumb {
      background: #bdc3c7; 
      border-radius: 10px;
    }
    ::-webkit-scrollbar-thumb:hover {
      background: #95a5a6; 
    }

    .user-assign-scroll {
      max-height: 200px;
      overflow-y: auto;
    }

    /* Mobile Sidebar & Overlay */
    .sidebar-overlay {
      position: fixed;
      top: 0; left: 0; right: 0; bottom: 0;
      background: rgba(0,0,0,0.5);
      z-index: 999;
      display: none;
      -webkit-backdrop-filter: blur(2px);
      backdrop-filter: blur(2px);
      opacity: 0;
      transition: opacity 0.3s ease;
    }
    .sidebar-overlay.show {
      display: block;
      opacity: 1;
    }

    .mobile-toggle {
      display: none;
      color: white;
      font-size: 1.5rem;
      cursor: pointer;
      transition: transform 0.2s;
    }
    .mobile-toggle:active {
      transform: scale(0.9);
    }

    @media (max-width: 991.98px) {
      .sidebar {
        transform: translateX(-100%);
        transition: transform 0.3s cubic-bezier(0.4, 0, 0.2, 1);
      }
      .sidebar.show-sidebar {
        transform: translateX(0);
        box-shadow: 0 0 50px rgba(0,0,0,0.2);
      }
      .main-content {
        margin-left: 0;
      }
      .mobile-toggle {
        display: block;
      }
      .app-header h1 {
        font-size: 1.4rem;
      }
    }

    /* Dark Mode Styles */
    body.dark-mode {
      --primary-color: #5dade2;
      --primary-dark: #3498db;
      --primary-color: #60a5fa;
      --primary-dark: #6366f1;
      --secondary-color: #bdc3c7;
      --body-bg: #121212;
      --card-bg: rgba(30, 30, 30, 0.8);
      --text-primary: #e0e0e0;
      --text-secondary: #a0a0a0;
      --text-primary: #f8f9fa;
      --text-secondary: #d1d5db;
    }
    
    body.dark-mode { color: #ffffff; }

    body.dark-mode .card-header {
      background-color: transparent;
      border-bottom-color: #333;
      color: var(--text-primary);
    }

    body.dark-mode .table-container {
      background-color: rgba(30, 30, 30, 0.6);
      border-color: #333;
    }

    body.dark-mode .table {
      color: var(--text-primary);
      --bs-table-color: var(--text-primary);
      --bs-table-bg: var(--card-bg);
      --bs-table-striped-bg: rgba(255,255,255,0.05);
      --bs-table-hover-bg: rgba(255,255,255,0.1);
      border-color: #333;
    }

    body.dark-mode .table thead th {
      background-color: #252525;
      color: var(--text-secondary);
      background-color: #2c2c2c;
      color: #ffffff;
      border-bottom-color: #333;
    }

    body.dark-mode .table tbody td {
      border-bottom-color: #333;
    }
    
    body.dark-mode .sidebar {
      background: rgba(25, 25, 25, 0.85);
      border-right: 1px solid rgba(255,255,255,0.1);
    }

    body.dark-mode .form-control,
    body.dark-mode .form-select {
      background-color: rgba(45, 45, 45, 0.8);
      border-color: #444;
      color: var(--text-primary);
    }

    body.dark-mode .form-control:focus,
    body.dark-mode .form-select:focus {
      background-color: #333;
      border-color: var(--primary-color);
      color: var(--text-primary);
    }

    body.dark-mode .dropzone,
    body.dark-mode .preview-container {
      background-color: #252525;
      border-color: #444;
    }

    body.dark-mode .modal-content {
      background-color: var(--card-bg);
      border-color: #444;
    }
    
    body.dark-mode .btn-close { filter: invert(1) grayscale(100%) brightness(200%); }
    body.dark-mode .list-group-item { background-color: var(--card-bg); color: var(--text-primary); border-color: #333; }
    body.dark-mode .input-group-text { border-color: #444; color: var(--text-secondary); }
    body.dark-mode .sidebar .nav-link:hover, 
    body.dark-mode .sidebar .nav-link.active { background-color: rgba(255, 255, 255, 0.1); }

    /* Readability improvements */
    body.dark-mode .text-muted { color: #adb5bd !important; }
    body.dark-mode .text-dark { color: #f8f9fa !important; }
    body.dark-mode h1, body.dark-mode h2, body.dark-mode h3, body.dark-mode h4, body.dark-mode h5, body.dark-mode h6 { color: var(--text-primary); }
    body.dark-mode .form-control::placeholder { color: #adb5bd; opacity: 1; }

    /* Mejoras UI/UX */
    .badge-status { padding: 0.5em 0.8em; border-radius: 50rem; font-weight: 500; font-size: 0.75em; }
    .bg-proceso { background-color: #fef3c7; color: #92400e; }
    .bg-archivado { background-color: #dcfce7; color: #166534; }
    body.dark-mode .bg-proceso { background-color: #4a3e16; color: #ffd900; }
    body.dark-mode .bg-archivado { background-color: #14402b; color: #2ecc71; }
    .storage-info { padding: 0 1rem 1rem; font-size: 0.75rem; color: var(--text-secondary); }
    .progress-storage { height: 6px; background-color: #e9ecef; border-radius: 10px; margin-top: 5px; overflow: hidden; }
    .progress-bar-storage { height: 100%; background: var(--gradient-primary); transition: width 0.5s ease; }

    /* Status Dots Indicators */
    .status-dot {
      height: 12px;
      width: 12px;
      border-radius: 50%;
      display: inline-block;
      margin-right: 8px;
      vertical-align: middle;
      box-shadow: 0 0 4px rgba(0,0,0,0.2);
    }
    .dot-yellow { background-color: #ffc107; }
    .dot-green { background-color: #198754; }
    .dot-red { background-color: #dc3545; }

    /* Firewall Styles */
    .firewall-alert {
      background: rgba(239, 68, 68, 0.1);
      border: 1px solid var(--danger-color);
      color: var(--danger-color);
      padding: 1rem;
      border-radius: 10px;
      margin-bottom: 1.5rem;
      font-size: 0.9rem;
      text-align: center;
      animation: fadeInScale 0.3s ease;
    }

    /* Dashboard Improvements */
    .welcome-card {
      background: #fff;
      border-left: 5px solid var(--primary-color);
      border-radius: var(--border-radius);
      position: relative;
      overflow: hidden;
    }
    .welcome-card::after {
      content: '';
      position: absolute;
      right: -20px;
      top: -50%;
      width: 200px;
      height: 200px;
      background: rgba(255,255,255,0.1);
      border-radius: 50%;
      pointer-events: none;
    }
    
    .stats-card {
      transition: all 0.3s cubic-bezier(0.25, 0.8, 0.25, 1);
      border: none;
      border-radius: 12px;
      overflow: hidden;
    }
    .stats-card:hover {
      transform: translateY(-4px);
      box-shadow: 0 10px 25px -5px rgba(0, 0, 0, 0.1);
    }
    .stats-icon-box {
      width: 48px; height: 48px; border-radius: 12px; display: flex; align-items: center; justify-content: center; font-size: 1.4rem;
    }
    .counter-value { font-size: 1.8rem; font-weight: 700; color: var(--text-primary); }
    .chart-container { position: relative; width: 100%; height: 320px; overflow: hidden; }

    /* New utility classes for inline styles fix */
    .bell-link {
      font-size: 1.5rem;
      text-decoration: none;
    }
    .pointer-cursor {
      cursor: pointer;
    }
    .preview-img {
      max-width: 100%;
      max-height: 200px;
    }
    .form-sm {
      width: 100%;
      padding: 0.25rem 0.5rem;
      font-size: 0.75rem;
      border-radius: 0.375rem;
    }
    .initially-hidden {
      display: none;
    }
    .actions-selector-container {
      max-height: 180px;
      overflow-y: auto;
    }
    
    /* Ensure consistent styling for user assignment containers */
    #asignacionUsuarios, #salidaAsignacionUsuarios, #editAsignacionUsuarios {
      max-height: 200px;
    }

    /* OCR Loading Overlay */
    .ocr-loading-overlay {
      position: absolute;
      top: 0; left: 0; right: 0; bottom: 0;
      background: rgba(255,255,255,0.9);
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      z-index: 10;
      border-radius: 12px;
    }
    .ocr-progress-container { width: 80%; height: 8px; background: #eee; border-radius: 4px; overflow: hidden; margin-top: 10px; }
    .ocr-progress-bar { height: 100%; background: var(--primary-color); width: 0%; transition: width 0.3s; }
  </style>
</head>
<body>
  <!-- Login Screen -->
  <div id="loginScreen" class="login-screen d-flex d-none">
    <div class="login-card">
      <div class="login-logo">
        <!-- CAMBIA EL SRC POR LA RUTA DE TU LOGO (ej. "img/mi-logo.png") -->
        <img id="loginLogoImg" src="https://via.placeholder.com/150?text=LOGO" alt="Logo Sistema">
        <h2>Sistema de Control</h2>
        <p>Documentos y Oficios</p>
      </div>
      <div id="firewallAlert" class="firewall-alert d-none">
        <i class="fas fa-shield-alt me-2"></i>
        <span id="firewallMessage">Acceso bloqueado temporalmente</span>
      </div>
      <form id="loginForm" class="login-form">
        <div id="loginError" class="login-error mb-3">
          <i class="fas fa-exclamation-circle me-2"></i>
          <span id="loginErrorText">Usuario o contraseña incorrectos</span>
        </div>
        <div class="mb-3">
          <label for="loginUser" class="form-label">Usuario</label>
          <div class="input-group">
            <span class="input-group-text"><i class="fas fa-user"></i></span>
            <input type="text" class="form-control" id="loginUser" placeholder="Ingrese su usuario" required>
          </div>
        </div>
        <div class="mb-3">
          <label for="loginPassword" class="form-label">Contraseña</label>
          <div class="input-group">
            <span class="input-group-text"><i class="fas fa-lock"></i></span>
            <input type="password" class="form-control" id="loginPassword" placeholder="Ingrese su contraseña" required>
          </div>
        </div>
        <div class="d-grid">
          <button type="submit" class="btn btn-primary btn-login">
            <i class="fas fa-sign-in-alt me-2"></i>Iniciar Sesión
          </button>
        </div>
      </form>
      <div class="text-center mt-4">
        <small class="text-muted">Usuario: admin | Contraseña: admin123</small>
      </div>
    </div>
  </div>

  <!-- App Content -->
  <div id="appContent" class="app-content">
    <div id="sidebarOverlay" class="sidebar-overlay"></div>
    <!-- Sidebar -->
    <div class="sidebar">
      <a class="sidebar-brand" href="javascript:void(0)">
        <!-- CAMBIA EL SRC POR LA RUTA DE TU LOGO PEQUEÑO -->
        <img id="sidebarLogoImg" src="https://via.placeholder.com/50?text=L" alt="Logo">
        <span>Sistema Control</span>
      </a>
      
      <ul class="nav flex-column mb-auto">
        <li class="nav-item">
          <a id="navInicio" class="nav-link active" href="javascript:void(0)">
            <i class="fas fa-home"></i> Inicio
          </a>
        </li>
        <li class="nav-item">
          <a id="navNuevoDocumento" class="nav-link" href="javascript:void(0)">
            <i class="fas fa-file-signature"></i> Doc. Entrada
          </a>
        </li>
        <li class="nav-item">
          <a id="navDocumentoSalida" class="nav-link" href="javascript:void(0)">
            <i class="fas fa-file-export"></i> Doc. Salida
          </a>
        </li>
        <li class="nav-item">
          <a id="navDocumentos" class="nav-link" href="javascript:void(0)">
            <i class="fas fa-file-alt"></i> Reg. Entrada
          </a>
        </li>
        <li class="nav-item">
          <a id="navDocumentosSalida" class="nav-link" href="javascript:void(0)">
            <i class="fas fa-file-export"></i> Reg. Salida
          </a>
        </li>
        <li class="nav-item">
          <a id="navConfiguracion" class="nav-link" href="javascript:void(0)" data-bs-toggle="modal" data-bs-target="#configModal">
            <i class="fas fa-cog"></i> Configuración
          </a>
        </li>
        <li class="nav-item">
          <a id="navUsuarios" class="nav-link" href="javascript:void(0)" data-bs-toggle="modal" data-bs-target="#usuariosModal">
            <i class="fas fa-users"></i> Usuarios
          </a>
        </li>
      </ul>
      
      <div class="mt-auto">
        <div class="storage-info mb-2">
          <div class="d-flex justify-content-between">
            <span>Almacenamiento Local</span>
            <span id="storagePercent">0%</span>
          </div>
      <div class="progress-storage"><div class="progress-bar-storage" id="storageBar"></div></div>
        </div>
        <a id="btnLogout" class="nav-link text-danger" href="javascript:void(0)">
          <i class="fas fa-sign-out-alt"></i> Salir
        </a>
      </div>
    </div>

    <!-- Main Content -->
    <div class="main-content">
      <header class="app-header">
        <div class="container position-relative d-flex align-items-center justify-content-center">
          <div class="position-absolute start-0 ms-3">
            <i class="fas fa-bars mobile-toggle" id="sidebarToggle"></i>
          </div>
          <h1 class="mb-0">Control y Registro de Documentos</h1>
          <div class="position-absolute top-50 end-0 translate-middle-y">
              <a id="navNotificaciones" class="text-white me-4" href="javascript:void(0)" data-bs-toggle="modal" data-bs-target="#notificacionesModal">
                <i class="fas fa-bell bell-link"></i>
              </a>
          </div>
        </div>
      </header>

      <main class="container-fluid px-4 my-5">
    <!-- Home section -->
    <div id="homeSection" class="my-4">
      <div class="card p-4 shadow-md welcome-card">
        <div class="row align-items-center">
          <div class="col-md-8">
            <h2>Bienvenido al Sistema de Control</h2>
            <p class="text-muted">Registra, consulta y gestiona tus oficios y documentos de forma segura con control de acceso por departamento.</p>
            <div class="d-flex gap-2">
              <button class="btn btn-outline-primary" id="btnViewDocs"><i class="fas fa-list me-2"></i>Ver Documentos</button>
              <button class="btn btn-outline-secondary" id="btnOpenConfig" data-bs-toggle="modal" data-bs-target="#configModal"><i class="fas fa-cog me-2"></i>Configuración</button>
            </div>
          </div>
          <div class="col-md-4">
            <div class="row">
              <div class="col-12 mb-2">
                <div class="card text-center border-0 shadow-sm">
                  <div class="card-body">
                    <div class="stats-card-icon bg-primary bg-opacity-10 text-primary"><i class="fas fa-file-alt"></i></div>
                    <h3 class="card-title fw-bold" id="homeTotalDocs">0</h3>
                    <p class="card-text text-muted">Documentos registrados</p>
                  </div>
                </div>
              </div>
              <div class="col-12">
                <div class="card text-center border-0 shadow-sm">
                  <div class="card-body">
                    <div class="stats-card-icon bg-success bg-opacity-10 text-success"><i class="fas fa-paperclip"></i></div>
                    <h3 class="card-title fw-bold" id="homeWithAttachments">0</h3>
                    <p class="card-text text-muted">Con adjuntos</p>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- Statistics Section -->
      <div class="row">
        <div class="col-md-6 mb-4">
          <div class="card h-100 shadow-sm">
            <div class="card-header bg-white fw-bold">
              <i class="fas fa-chart-pie me-2 text-primary"></i>Documentos por Tipo
            </div>
            <div class="card-body chart-container">
              <canvas id="chartTipo"></canvas>
            </div>
          </div>
        </div>
        <div class="col-md-6 mb-4">
          <div class="card h-100 shadow-sm">
            <div class="card-header bg-white fw-bold">
              <i class="fas fa-chart-bar me-2 text-success"></i>Documentos por Asunto
            </div>
            <div class="card-body chart-container">
              <canvas id="chartAsunto"></canvas>
            </div>
          </div>
        </div>
      </div>

      <!-- Recent Activity Section -->
      <div class="row mb-4">
        <div class="col-12">
          <div class="card shadow-sm">
            <div class="card-header bg-white fw-bold d-flex justify-content-between align-items-center">
              <span><i class="fas fa-history me-2 text-info"></i>Actividad Reciente</span>
              <small class="text-muted fw-normal">Últimos 5 registros</small>
            </div>
            <div class="card-body p-0">
              <div class="table-responsive">
                <table class="table table-hover mb-0 align-middle">
                  <thead class="table-light">
                    <tr>
                      <th class="ps-4">Oficio</th>
                      <th>Asunto</th>
                      <th>Tipo</th>
                      <th>Fecha</th>
                      <th>Estado</th>
                    </tr>
                  </thead>
                  <tbody id="recentActivityTable">
                    <tr><td colspan="5" class="text-center py-3">No hay actividad reciente</td></tr>
                  </tbody>
                </table>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- Documento de Entrada -->
    <div id="registerSection" class="my-4 d-none">
      <div class="card">
        <div class="card-header">
          <i class="fas fa-file-signature me-2"></i>Documento de Entrada
        </div>
        <div class="card-body">
          <form id="documentForm">
            <div class="row mb-3">
              <div class="col-md-8">
                <label for="destinatario" class="form-label required">Unidad de Procedencia</label>
                <input type="text" class="form-control" id="destinatario" required>
              </div>
              <div class="col-md-4">
                <label for="fecha" class="form-label required">Fecha de Recepción</label>
                <input type="text" class="form-control" id="fecha" required>
              </div>
            </div>

            <div class="row mb-3">
              <div class="col-md-4">
                <label for="numeroOficio" class="form-label">N° de Oficio</label>
                <input type="text" class="form-control" id="numeroOficio" placeholder="Número de oficio">
              </div>
              <div class="col-md-4">
                <label for="tipoDocumento" class="form-label required">Tipo de Documento</label>
                <select class="form-select" id="tipoDocumento" required>
                  <option value="">Seleccione un tipo</option>
                  <option value="oficio">Oficio</option>
                  <option value="memorandum">Memorándum</option>
                  <option value="circular">Circular</option>
                  <option value="informe">Informe</option>
                  <option value="agenda">Agenda</option>
                  <option value="radiograma">Radiograma</option>
                  <option value="punto_cuenta">Punto de Cuenta</option>
                  <option value="acta">Acta</option>
                  <option value="otro">Otro</option>
                </select>
              </div>
              <div class="col-md-4">
                <label for="asunto" class="form-label required">Asunto del Documento</label>
                <select class="form-select" id="asunto" required>
                  <option value="">Seleccione un asunto</option>
                  <option value="presentacion">Presentación</option>
                  <option value="informacion">Información</option>
                  <option value="remision">Remisión</option>
                  <option value="acuse">Acuse de Recibo</option>
                  <option value="solicitud">Solicitud</option>
                </select>
              </div>
            </div>

            <div class="row mb-3">
              <div class="col-md-6">
<label class="form-label required" id="lblAsignacionEntrada">División Asignada (Selección Múltiple)</label>
<div id="asignacionUsuarios" class="border rounded p-3 bg-white user-assign-scroll">
                    <p class="text-muted small mb-0">Cargando usuarios...</p>
                  </div>
              </div>
              <div class="col-md-6">
                <label class="form-label">Acción (Selección Múltiple)</label>
                <div class="border rounded p-3 bg-white actions-selector-container" id="accion"></div>
              </div>
              <div class="col-md-6 mt-3">
                <label for="profesionalRegistro" class="form-label">Profesional que registró</label>
                <input type="text" class="form-control" id="profesionalRegistro" placeholder="Nombre del profesional">
              </div>
            </div>

            <div class="mb-3">
              <label for="descripcion" class="form-label required">Resumen o Descripción</label>
              <textarea class="form-control" id="descripcion" rows="3" required></textarea>
            </div>

            <div class="card mb-3">
              <div class="card-header bg-light">
                <i class="fas fa-file-upload me-2"></i>Documento Adjunto
              </div>
              <div class="card-body">
                <div class="mb-3">
                  <div class="dropzone" id="dropzone">
                    <input type="file" id="documentFile" class="d-none" accept=".pdf,.jpg,.jpeg,.png" multiple>
                    <i class="fas fa-cloud-upload-alt"></i>
                    <p class="mb-0">Arrastra aquí tus archivos o haz clic para seleccionarlos</p>
                    <small class="text-muted d-block mt-2">Archivos aceptados: PDF, JPG, PNG</small>
                  </div>
                  <div class="ocr-loading-overlay d-none" id="ocrLoader">
                    <div class="spinner-border text-primary" role="status"></div>
                    <span class="mt-2 fw-bold">Analizando documento...</span>
                    <div class="ocr-progress-container">
                      <div class="ocr-progress-bar" id="ocrProgressBar"></div>
                    </div>
                    <small class="text-muted" id="ocrStatus">Inicializando IA...</small>
                  </div>
                  <div class="text-center mt-3 d-flex justify-content-center gap-2">
                    <button type="button" class="btn btn-sm btn-outline-info" onclick="openScanner('documentFile', 'previewContainer', 'previewContent')"><i class="fas fa-camera me-1"></i>Cámara</button>
                    <button type="button" class="btn btn-sm btn-outline-dark" onclick="scanFromPhysicalScanner('documentFile', 'previewContainer', 'previewContent')">
                      <i class="fas fa-print me-1"></i>Escáner Físico
                    </button>
                    <button type="button" class="btn btn-sm btn-primary d-none" id="btnSmartFill" onclick="processDocumentExtraction('documentFile', 'entrada')">
                      <i class="fas fa-magic me-1"></i>Auto-completar campos
                    </button>
                  </div>
                </div>
                <div class="preview-container d-none mt-3" id="previewContainer">
                  <div id="previewContent"></div>
                </div>
              </div>
            </div>

            <div class="d-grid gap-2 d-md-flex justify-content-md-end">
              <button type="reset" class="btn btn-outline-secondary">
                <i class="fas fa-undo-alt me-2"></i>Limpiar
              </button>
              <button type="button" class="btn btn-primary" id="btnRegistrar">
                <i class="fas fa-save me-2"></i>Registrar Documento
              </button>
            </div>
          </form>
        </div>
      </div>
    </div>

    <!-- Documento de Salida -->
    <div id="salidaSection" class="my-4 d-none">
      <div class="card">
        <div class="card-header">
          <i class="fas fa-file-export me-2"></i>Documento de Salida
        </div>
        <div class="card-body">
          <form id="salidaForm">
            <div class="row mb-3">
              <div class="col-md-8">
                <label for="salidaDestinatario" class="form-label required">Unidad de Destino</label>
                <input type="text" class="form-control" id="salidaDestinatario" required>
              </div>
              <div class="col-md-4">
                <label for="salidaFecha" class="form-label required">Fecha de Emisión</label>
                <input type="text" class="form-control" id="salidaFecha" required>
              </div>
            </div>

            <div class="row mb-3">
              <div class="col-md-4">
                <label for="salidaNumeroOficio" class="form-label">N° de Oficio</label>
                <input type="text" class="form-control" id="salidaNumeroOficio" placeholder="Número de oficio">
              </div>
              <div class="col-md-4">
                <label for="salidaTipoDocumento" class="form-label required">Tipo de Documento</label>
                <select class="form-select" id="salidaTipoDocumento" required>
                  <option value="">Seleccione un tipo</option>
                  <option value="oficio">Oficio</option>
                  <option value="memorandum">Memorándum</option>
                  <option value="circular">Circular</option>
                  <option value="informe">Informe</option>
                  <option value="agenda">Agenda</option>
                  <option value="radiograma">Radiograma</option>
                  <option value="punto_cuenta">Punto de Cuenta</option>
                  <option value="acta">Acta</option>
                  <option value="otro">Otro</option>
                </select>
              </div>
              <div class="col-md-4">
                <label for="salidaAsunto" class="form-label required">Asunto del Documento</label>
                <select class="form-select" id="salidaAsunto" required>
                  <option value="">Seleccione un asunto</option>
                  <option value="presentacion">Presentación</option>
                  <option value="informacion">Información</option>
                  <option value="remision">Remisión</option>
                  <option value="acuse">Acuse de Recibo</option>
                  <option value="solicitud">Solicitud</option>
                </select>
              </div>
            </div>

            <div class="mb-3">
              <label for="salidaDescripcion" class="form-label required">Resumen o Descripción</label>
              <textarea class="form-control" id="salidaDescripcion" rows="3" required></textarea>
            </div>

            <div class="row mb-3">
              <div class="col-md-6">
<label class="form-label required" id="lblAsignacionSalida">División Asignada (Selección Múltiple)</label>
<div id="salidaAsignacionUsuarios" class="border rounded p-3 bg-white user-assign-scroll">
                    <p class="text-muted small mb-0">Cargando usuarios...</p>
                  </div>
              </div>
              <div class="col-md-6">
                <label class="form-label">Acción (Selección Múltiple)</label>
                <div class="border rounded p-3 bg-white actions-selector-container" id="salidaAccion"></div>
              </div>
              <div class="col-md-6">
                <label for="salidaProfesional" class="form-label">Profesional que lo elaboró</label>
                <input type="text" class="form-control" id="salidaProfesional" placeholder="Nombre del profesional">
              </div>
            </div>

            <div class="mb-3">
              <label for="salidaFechaEntrega" class="form-label">Fecha de Entrega</label>
              <input type="text" class="form-control" id="salidaFechaEntrega" placeholder="Selecciona la fecha de entrega">
            </div>

            <div class="card mb-3">
              <div class="card-header bg-light">
                <i class="fas fa-file-upload me-2"></i>Documento Adjunto
              </div>
              <div class="card-body">
                <div class="mb-3">
                  <div class="dropzone" id="salidaDropzone">
                    <input type="file" id="salidaDocumentFile" class="d-none" accept=".pdf,.jpg,.jpeg,.png" multiple>
                    <i class="fas fa-cloud-upload-alt"></i>
                    <p class="mb-0">Arrastra aquí tus archivos o haz clic para seleccionarlos</p>
                    <small class="text-muted d-block mt-2">Archivos aceptados: PDF, JPG, PNG</small>
                  </div>
                  <div class="ocr-loading-overlay d-none" id="ocrLoaderSalida">
                    <div class="spinner-border text-success" role="status"></div>
                    <span class="mt-2 fw-bold">Analizando documento...</span>
                    <div class="ocr-progress-container">
                      <div class="ocr-progress-bar" id="ocrProgressBarSalida"></div>
                    </div>
                  </div>
                  <div class="text-center mt-3 d-flex justify-content-center gap-2">
                    <button type="button" class="btn btn-sm btn-outline-info" onclick="openScanner('salidaDocumentFile', 'salidaPreviewContainer', 'salidaPreviewContent')"><i class="fas fa-camera me-1"></i>Cámara</button>
                    <button type="button" class="btn btn-sm btn-outline-dark" onclick="scanFromPhysicalScanner('salidaDocumentFile', 'salidaPreviewContainer', 'salidaPreviewContent')">
                      <i class="fas fa-print me-1"></i>Escáner Físico
                    </button>
                    <button type="button" class="btn btn-sm btn-primary d-none" id="btnSmartFillSalida" onclick="processDocumentExtraction('salidaDocumentFile', 'salida')">
                      <i class="fas fa-magic me-1"></i>Auto-completar campos
                    </button>
                  </div>
                </div>
                <div class="preview-container d-none mt-3" id="salidaPreviewContainer">
                  <div id="salidaPreviewContent"></div>
                </div>
              </div>
            </div>

            <div class="d-grid gap-2 d-md-flex justify-content-md-end">
              <button type="reset" class="btn btn-outline-secondary">
                <i class="fas fa-undo-alt me-2"></i>Limpiar
              </button>
              <button type="button" class="btn btn-primary" id="btnRegistrarSalida">
                <i class="fas fa-save me-2"></i>Registrar Documento de Salida
              </button>
            </div>
          </form>
        </div>
      </div>
    </div>

    <!-- Registro de Entrada -->
    <div id="documentsSection" class="d-none">
      <div class="card">
        <div class="card-header d-flex justify-content-between align-items-center">
          <div>
            <i class="fas fa-list me-2"></i>Documentos Registrados - Entrada
          </div>
          <div class="d-flex gap-2">
            <div class="dropdown">
              <button class="btn btn-sm btn-outline-info dropdown-toggle" type="button" data-bs-toggle="dropdown">
                <i class="fas fa-calendar-alt me-2"></i>Relación
              </button>
              <ul class="dropdown-menu">
                <li><a class="dropdown-item" href="javascript:void(0)" onclick="exportPeriodico('entrada', 'semanal')">Semanal</a></li>
                <li><a class="dropdown-item" href="javascript:void(0)" onclick="exportPeriodico('entrada', 'quincenal')">Quincenal</a></li>
                <li><a class="dropdown-item" href="javascript:void(0)" onclick="exportPeriodico('entrada', 'mensual')">Mensual</a></li>
              </ul>
            </div>
            <button class="btn btn-sm btn-outline-success" id="exportarExcel">
              <i class="fas fa-file-excel me-2"></i>Exportar Excel
            </button>
          </div>
        </div>
        <div class="card-body">
          <div class="search-form">
            <div class="row">
              <div class="col-md-9">
                <div class="input-group">
                  <input type="text" class="form-control" placeholder="Buscar por departamento, número de oficio..." id="searchInput">
                  <button class="btn btn-outline-secondary" type="button" id="btnSearch">
                    <i class="fas fa-search"></i>
                  </button>
                </div>
              </div>
              <div class="col-md-3">
                <select class="form-select" id="filterDate">
                  <option value="all">Todas las fechas</option>
                  <option value="today">Hoy</option>
                  <option value="week">Esta semana</option>
                  <option value="month">Este mes</option>
                  <option value="year">Este año</option>
                </select>
              </div>
            </div>
          </div>

          <div id="loader" class="loader">
            <i class="fas fa-circle-notch fa-spin"></i>
            <p>Cargando documentos...</p>
          </div>

          <div class="table-container">
            <table class="table table-striped table-hover">
              <thead class="table-light">
                <tr>
                  <th scope="col" class="text-nowrap">N°</th>
                  <th scope="col" class="text-nowrap">Número de Oficio</th>
                  <th scope="col" class="text-nowrap">Asunto</th>
                  <th scope="col" class="text-nowrap">Tipo de Documento</th>
                  <th scope="col" class="text-nowrap">Fecha de Recepción</th>
                  <th scope="col" class="text-nowrap">Unidad de Procedencia</th>
                  <th scope="col" class="text-nowrap">Profesional que registró</th>
                  <th scope="col" class="text-nowrap">Asignación</th>
                  <th scope="col" class="text-nowrap">Acción</th>
                  <th scope="col" class="text-nowrap">Resumen/Descripción</th>
                  <th scope="col" class="text-nowrap">Archivo Adjunto</th>
                  <th scope="col" class="text-nowrap">Acciones</th>
                  <th scope="col" class="text-nowrap">Observación</th>
                  <th scope="col" class="text-nowrap">Condición</th>
                </tr>
              </thead>
              <tbody id="documentosTableBody">
                <tr class="text-center">
                  <td colspan="15">No hay documentos registrados</td>
                </tr>
              </tbody>
            </table>
          </div>
          <!-- Pagination Entrada -->
          <nav aria-label="Navegación de documentos" class="mt-3 d-none" id="paginationContainerEntrada">
            <ul class="pagination justify-content-center" id="paginationEntrada"></ul>
          </nav>
        </div>
      </div>
    </div>

    <!-- Registro de Salida -->
    <div id="salidaDocumentsSection" class="d-none">
      <div class="card">
        <div class="card-header d-flex justify-content-between align-items-center">
          <div>
            <i class="fas fa-file-export me-2"></i>Registro de Salida
          </div>
          <div class="d-flex gap-2">
            <div class="dropdown">
              <button class="btn btn-sm btn-outline-info dropdown-toggle" type="button" data-bs-toggle="dropdown">
                <i class="fas fa-calendar-alt me-2"></i>Relación
              </button>
              <ul class="dropdown-menu">
                <li><a class="dropdown-item" href="javascript:void(0)" onclick="exportPeriodico('salida', 'semanal')">Semanal</a></li>
                <li><a class="dropdown-item" href="javascript:void(0)" onclick="exportPeriodico('salida', 'quincenal')">Quincenal</a></li>
                <li><a class="dropdown-item" href="javascript:void(0)" onclick="exportPeriodico('salida', 'mensual')">Mensual</a></li>
              </ul>
            </div>
            <button class="btn btn-sm btn-outline-success" id="exportarExcelSalida">
              <i class="fas fa-file-excel me-2"></i>Exportar Excel
            </button>
          </div>
        </div>
        <div class="card-body">
          <div class="search-form">
            <div class="row">
              <div class="col-md-9">
                <div class="input-group">
                  <input type="text" class="form-control" placeholder="Buscar por departamento, número de oficio..." id="searchInputSalida">
                  <button class="btn btn-outline-secondary" type="button" id="btnSearchSalida">
                    <i class="fas fa-search"></i>
                  </button>
                </div>
              </div>
              <div class="col-md-3">
                <select class="form-select" id="filterDateSalida">
                  <option value="all">Todas las fechas</option>
                  <option value="today">Hoy</option>
                  <option value="week">Esta semana</option>
                  <option value="month">Este mes</option>
                  <option value="year">Este año</option>
                </select>
              </div>
            </div>
          </div>

          <div id="loaderSalida" class="loader">
            <i class="fas fa-circle-notch fa-spin"></i>
            <p>Cargando documentos de salida...</p>
          </div>

          <div class="table-container">
            <table class="table table-striped table-hover">
              <thead class="table-light">
                <tr>
                  <th scope="col" class="text-nowrap">N°</th>
                  <th scope="col" class="text-nowrap">Número de Oficio</th>
                  <th scope="col" class="text-nowrap">Asunto</th>
                  <th scope="col" class="text-nowrap">Tipo de Documento</th>
                  <th scope="col" class="text-nowrap">Fecha de Emisión</th>
                  <th scope="col" class="text-nowrap">Unidad de Destino</th>
                  <th scope="col" class="text-nowrap">Asignación</th>
                  <th scope="col" class="text-nowrap">Acción</th>
                  <th scope="col" class="text-nowrap">Profesional que lo elaboró</th>
                  <th scope="col" class="text-nowrap">Fecha de Entrega</th>
                  <th scope="col" class="text-nowrap">Resumen/Descripción</th>
                  <th scope="col" class="text-nowrap">Archivo Adjunto</th>
                  <th scope="col" class="text-nowrap">Acciones</th>
                  <th scope="col" class="text-nowrap">Observación</th>
                  <th scope="col" class="text-nowrap">Condición</th>
                </tr>
              </thead>
              <tbody id="salidaDocumentosTableBody">
                <tr class="text-center">
                  <td colspan="17">No hay documentos de salida registrados</td>
                </tr>
              </tbody>
            </table>
          </div>
          <!-- Pagination Salida -->
          <nav aria-label="Navegación de documentos salida" class="mt-3 d-none" id="paginationContainerSalida">
            <ul class="pagination justify-content-center" id="paginationSalida"></ul>
          </nav>
        </div>
      </div>
    </div>
      </main>
    </div>

  <!-- Modal para editar documento -->
  <div class="modal fade" id="editarDocumentoModal">
    <div class="modal-dialog modal-lg">
      <div class="modal-content">
        <div class="modal-header">
          <h5 class="modal-title">Editar Documento</h5>
          <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
        </div>
        <div class="modal-body">
          <form id="editDocumentForm">
            <input type="hidden" id="editId">
            <input type="hidden" id="editType" value="entrada">
            <div class="row mb-3">
              <div class="col-md-8">
                <label for="editDestinatario" class="form-label required">Unidad de Procedencia/Destino</label>
                <input type="text" class="form-control" id="editDestinatario" required>
              </div>
              <div class="col-md-4">
                <label for="editFecha" class="form-label required">Fecha de Recepción/Emisión</label>
                <input type="text" class="form-control" id="editFecha" required>
              </div>
            </div>

            <div class="row mb-3">
              <div class="col-md-4">
                <label for="editNumeroOficio" class="form-label">N° de Oficio</label>
                <input type="text" class="form-control" id="editNumeroOficio" placeholder="Número de oficio">
              </div>
              <div class="col-md-4">
                <label for="editTipoDocumento" class="form-label required">Tipo de Documento</label>
                <select class="form-select" id="editTipoDocumento" required>
                  <option value="">Seleccione un tipo</option>
                  <option value="oficio">Oficio</option>
                  <option value="memorandum">Memorándum</option>
                  <option value="circular">Circular</option>
                  <option value="informe">Informe</option>
                  <option value="agenda">Agenda</option>
                  <option value="radiograma">Radiograma</option>
                  <option value="punto_cuenta">Punto de Cuenta</option>
                  <option value="acta">Acta</option>
                  <option value="otro">Otro</option>
                </select>
              </div>
              <div class="col-md-4">
                <label for="editAsunto" class="form-label required">Asunto del Documento</label>
                <select class="form-select" id="editAsunto" required>
                  <option value="">Seleccione un asunto</option>
                  <option value="presentacion">Presentación</option>
                  <option value="informacion">Información</option>
                  <option value="remision">Remisión</option>
                  <option value="acuse">Acuse de Recibo</option>
                  <option value="solicitud">Solicitud</option>
                </select>
              </div>
            </div>

            <div class="row mb-3">
              <div class="col-md-6">
<label class="form-label required" id="lblAsignacionEdit">División Asignada (Selección Múltiple)</label>
<div id="editAsignacionUsuarios" class="border rounded p-3 bg-white user-assign-scroll">
                    <p class="text-muted small mb-0">Cargando usuarios...</p>
                  </div>
              </div>
              <div class="col-md-6">
                <label class="form-label">Acción (Selección Múltiple)</label>
                <div class="border rounded p-3 bg-white actions-selector-container" id="editAccion"></div>
              </div>
            </div>

            <div class="mb-3 d-none" id="editFechaEntregaContainer">
              <label for="editFechaEntrega" class="form-label">Fecha de Entrega</label>
              <input type="text" class="form-control" id="editFechaEntrega" placeholder="Selecciona la fecha de entrega">
            </div>

            <div class="mb-3 d-none" id="editProfesionalContainer">
              <label for="editProfesional" class="form-label">Profesional que lo elaboró</label>
              <input type="text" class="form-control" id="editProfesional">
            </div>

            <div class="mb-3 d-none" id="editProfesionalRegistroContainer">
              <label for="editProfesionalRegistro" class="form-label">Profesional que registró</label>
              <input type="text" class="form-control" id="editProfesionalRegistro">
            </div>

            <div class="mb-3">
              <label for="editDescripcion" class="form-label required">Resumen o Descripción</label>
              <textarea class="form-control" id="editDescripcion" rows="3" required></textarea>
            </div>
          </form>
        </div>
        <div class="modal-footer">
          <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Cancelar</button>
          <button type="button" class="btn btn-primary" id="btnSaveEdit">Guardar Cambios</button>
        </div>
      </div>
    </div>
  </div>

  <!-- Modal para configuración -->
  <div class="modal fade" id="configModal">
    <div class="modal-dialog">
      <div class="modal-content">
        <div class="modal-header">
          <h5 class="modal-title">Configuración</h5>
          <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
        </div>
        <div class="modal-body">
          <p>Configuraciones del sistema.</p>
          <h6 class="mb-3">Apariencia</h6>
          <div class="form-check form-switch mb-4">
            <input class="form-check-input" type="checkbox" id="darkModeToggle">
            <label class="form-check-label" for="darkModeToggle">Modo Oscuro</label>
          </div>

          <h6 class="mb-3">Personalización</h6>
          <div class="mb-3">
            <label for="logoUpload" class="form-label">Logotipo del Sistema</label>
            <input class="form-control" type="file" id="logoUpload" accept="image/png, image/jpeg, image/jpg">
            <div class="form-text">Se recomienda una imagen PNG con fondo transparente (Máx. 500KB).</div>
          </div>
          <div class="d-grid">
            <button class="btn btn-outline-danger btn-sm" id="btnResetLogo">
              <i class="fas fa-trash-alt me-2"></i>Restaurar Logo Predeterminado
            </button>
          </div>

          <hr class="my-4">
          <h6 class="mb-3">Estructura Organizativa</h6>
          <p class="small text-muted">Visualice y gestione las divisiones y departamentos del sistema.</p>
          <div class="d-grid">
            <button class="btn btn-primary" id="btnOpenOrganigrama">
              <i class="fas fa-sitemap me-2"></i>Ver y Editar Organigrama
            </button>
          </div>
        </div>
        <div class="modal-footer">
          <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Cerrar</button>
        </div>
      </div>
    </div>
  </div>

  <!-- Modal para usuarios -->
  <div class="modal fade" id="usuariosModal">
    <div class="modal-dialog modal-lg">
      <div class="modal-content">
        <div class="modal-header">
          <h5 class="modal-title">Gestión de Usuarios</h5>
          <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
        </div>
        <div class="modal-body">
          <button class="btn btn-primary mb-3" data-bs-toggle="modal" data-bs-target="#addUserModal">
            <i class="fas fa-plus me-2"></i>Agregar Usuario
          </button>
          <div class="table-responsive">
            <table class="table table-striped">
              <thead>
                <tr>
                  <th>Usuario</th>
                  <th>División</th>
                  <th>Departamento</th>
                  <th>Rol</th>
                  <th>Acciones</th>
                </tr>
              </thead>
              <tbody id="usuariosTableBody">
                <tr>
                  <td>admin</td>
                  <td>Admin</td>
                  <td>
                    <button class="btn btn-sm btn-warning" disabled>Editar</button>
                    <button class="btn btn-sm btn-danger" disabled>Eliminar</button>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
        <div class="modal-footer">
          <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Cerrar</button>
        </div>
      </div>
    </div>
  </div>

  <!-- Modal para agregar usuario -->
  <div class="modal fade" id="addUserModal">
    <div class="modal-dialog">
      <div class="modal-content">
        <div class="modal-header">
          <h5 class="modal-title">Agregar Usuario</h5>
          <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
        </div>
        <div class="modal-body">
          <form id="addUserForm">
            <div class="mb-3">
              <label for="newUsername" class="form-label">Usuario</label>
              <input type="text" class="form-control" id="newUsername" required>
            </div>
            <div class="mb-3">
              <label for="newPassword" class="form-label">Contraseña</label>
              <input type="password" class="form-control" id="newPassword" required>
            </div>
            <div class="mb-3">
              <label for="newRole" class="form-label">Rol</label>
              <select class="form-select" id="newRole" required>
                <option value="viewer">Viewer</option>
                <option value="editor">Editor</option>
                <option value="admin">Admin</option>
                <option value="jefe_division">Jefe de División</option>
                <option value="usuario_dep">Usuario de Departamento</option>
              </select>
            </div>
            <div class="mb-3 d-none" id="newDivisionContainer">
              <label for="newDivision" class="form-label">División</label>
              <select class="form-select" id="newDivision">
                <!-- Dinámico -->
              </select>
            </div>

            <div class="mb-3 d-none" id="newDepartamentoContainer">
              <label for="newDepartamento" class="form-label">Departamento</label>
              <select class="form-select" id="newDepartamento">
                <option value="">Seleccione un departamento</option>
                <!-- Dinámico -->
              </select>
            </div>
            <div class="mb-3">
              <label class="form-label">Permisos de Menú</label>
              <div class="form-check">
                <input class="form-check-input perm-checkbox" type="checkbox" value="navInicio" id="perm_navInicio_new" checked>
                <label class="form-check-label" for="perm_navInicio_new">Inicio</label>
              </div>
              <div class="form-check">
                <input class="form-check-input perm-checkbox" type="checkbox" value="navNuevoDocumento" id="perm_navNuevoDocumento_new">
                <label class="form-check-label" for="perm_navNuevoDocumento_new">Doc. Entrada</label>
              </div>
              <div class="form-check">
                <input class="form-check-input perm-checkbox" type="checkbox" value="navDocumentoSalida" id="perm_navDocumentoSalida_new">
                <label class="form-check-label" for="perm_navDocumentoSalida_new">Doc. Salida</label>
              </div>
              <div class="form-check">
                <input class="form-check-input perm-checkbox" type="checkbox" value="navDocumentos" id="perm_navDocumentos_new">
                <label class="form-check-label" for="perm_navDocumentos_new">Reg. Entrada</label>
              </div>
              <div class="form-check">
                <input class="form-check-input perm-checkbox" type="checkbox" value="navDocumentosSalida" id="perm_navDocumentosSalida_new">
                <label class="form-check-label" for="perm_navDocumentosSalida_new">Reg. Salida</label>
              </div>
              <div class="form-check">
                <input class="form-check-input perm-checkbox" type="checkbox" value="navConfiguracion" id="perm_navConfiguracion_new">
                <label class="form-check-label" for="perm_navConfiguracion_new">Configuración</label>
              </div>
              <div class="form-check">
                <input class="form-check-input perm-checkbox" type="checkbox" value="navUsuarios" id="perm_navUsuarios_new">
                <label class="form-check-label" for="perm_navUsuarios_new">Usuarios</label>
              </div>
              <div class="form-check">
                <input class="form-check-input perm-checkbox" type="checkbox" value="navNotificaciones" id="perm_navNotificaciones_new" checked>
                <label class="form-check-label" for="perm_navNotificaciones_new">Notificaciones</label>
              </div>
            </div>
          </form>
        </div>
        <div class="modal-footer">
          <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Cancelar</button>
          <button type="button" class="btn btn-primary" id="btnAddUser">Agregar</button>
        </div>
      </div>
    </div>
  </div>

  <!-- Modal para editar usuario -->
  <div class="modal fade" id="editUserModal">
    <div class="modal-dialog">
      <div class="modal-content">
        <div class="modal-header">
          <h5 class="modal-title">Editar Usuario</h5>
          <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
        </div>
        <div class="modal-body">
          <form id="editUserForm">
            <input type="hidden" id="editUserIndex">
            <div class="mb-3">
              <label for="editUsername" class="form-label">Usuario</label>
              <input type="text" class="form-control" id="editUsername" required>
            </div>
            <div class="mb-3">
              <label for="editPassword" class="form-label">Contraseña</label>
              <input type="password" class="form-control" id="editPassword" required>
            </div>
            <div class="mb-3">
              <label for="editRole" class="form-label">Rol</label>
              <select class="form-select" id="editRole" required>
                <option value="viewer">Viewer</option>
                <option value="editor">Editor</option>
                <option value="admin">Admin</option>
                <option value="jefe_division">Jefe de División</option>
                <option value="usuario_dep">Usuario de Departamento</option>
              </select>
            </div>
            <div class="mb-3 d-none" id="editDivisionContainer">
              <label for="editDivision" class="form-label">División</label>
              <select class="form-select" id="editDivision">
                <!-- Dinámico -->
              </select>
            </div>
            <div class="mb-3 d-none" id="editDepartamentoContainer">
              <label for="editDepartamento" class="form-label">Departamento</label>
              <select class="form-select" id="editDepartamento">
                <option value="">Seleccione un departamento</option>
                <!-- Dinámico -->
              </select>
            </div>
            <div class="mb-3">
              <label class="form-label">Permisos de Menú</label>
              <div class="form-check">
                <input class="form-check-input perm-checkbox" type="checkbox" value="navInicio" id="perm_navInicio_edit">
                <label class="form-check-label" for="perm_navInicio_edit">Inicio</label>
              </div>
              <div class="form-check">
                <input class="form-check-input perm-checkbox" type="checkbox" value="navNuevoDocumento" id="perm_navNuevoDocumento_edit">
                <label class="form-check-label" for="perm_navNuevoDocumento_edit">Doc. Entrada</label>
              </div>
              <div class="form-check">
                <input class="form-check-input perm-checkbox" type="checkbox" value="navDocumentoSalida" id="perm_navDocumentoSalida_edit">
                <label class="form-check-label" for="perm_navDocumentoSalida_edit">Doc. Salida</label>
              </div>
              <div class="form-check">
                <input class="form-check-input perm-checkbox" type="checkbox" value="navDocumentos" id="perm_navDocumentos_edit">
                <label class="form-check-label" for="perm_navDocumentos_edit">Reg. Entrada</label>
              </div>
              <div class="form-check">
                <input class="form-check-input perm-checkbox" type="checkbox" value="navDocumentosSalida" id="perm_navDocumentosSalida_edit">
                <label class="form-check-label" for="perm_navDocumentosSalida_edit">Reg. Salida</label>
              </div>
              <div class="form-check">
                <input class="form-check-input perm-checkbox" type="checkbox" value="navConfiguracion" id="perm_navConfiguracion_edit">
                <label class="form-check-label" for="perm_navConfiguracion_edit">Configuración</label>
              </div>
              <div class="form-check">
                <input class="form-check-input perm-checkbox" type="checkbox" value="navUsuarios" id="perm_navUsuarios_edit">
                <label class="form-check-label" for="perm_navUsuarios_edit">Usuarios</label>
              </div>
              <div class="form-check">
                <input class="form-check-input perm-checkbox" type="checkbox" value="navNotificaciones" id="perm_navNotificaciones_edit">
                <label class="form-check-label" for="perm_navNotificaciones_edit">Notificaciones</label>
              </div>
            </div>
          </form>
        </div>
        <div class="modal-footer">
          <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Cancelar</button>
          <button type="button" class="btn btn-primary" id="btnSaveEditUser">Guardar Cambios</button>
        </div>
      </div>
    </div>
  </div>

  <!-- Modal para notificaciones -->
  <div class="modal fade" id="notificacionesModal">
    <div class="modal-dialog modal-lg">
      <div class="modal-content">
        <div class="modal-header">
          <h5 class="modal-title">Historial de Notificaciones</h5>
          <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
        </div>
        <div class="modal-body">
          <div class="d-flex justify-content-end mb-3">
            <button class="btn btn-sm btn-outline-danger" id="btnClearNotifications">
              <i class="fas fa-trash-alt me-2"></i>Limpiar Historial
            </button>
          </div>
          <div class="list-group" id="notificationsList">
            <div class="text-center text-muted p-3">No hay notificaciones</div>
          </div>
        </div>
        <div class="modal-footer">
          <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Cerrar</button>
        </div>
      </div>
    </div>
  </div>

  <!-- Modal para Organigrama -->
  <div class="modal fade" id="organigramaModal">
    <div class="modal-dialog modal-xl">
      <div class="modal-content">
        <div class="modal-header">
          <h5 class="modal-title"><i class="fas fa-sitemap me-2"></i>Estructura Organizativa</h5>
          <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
        </div>
        <div class="modal-body">
          <div id="adminStructureControls" class="mb-4 p-3 border rounded bg-light d-none">
            <h6>Panel de Administración de Estructura</h6>
            <div class="row g-3 align-items-center">
              <div class="col-auto">
                <input type="text" id="newDivisionName" class="form-control" placeholder="Nombre de nueva división">
              </div>
              <div class="col-auto">
                <button class="btn btn-success" onclick="addDivisionStructure()">Añadir División</button>
              </div>
            </div>
          </div>
          <div id="organigramaContainer">
            <!-- Se llena dinámicamente -->
          </div>
        </div>
        <div class="modal-footer">
          <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Cerrar</button>
        </div>
      </div>
    </div>
  </div>

  <!-- Modal para Escáner de Cámara -->
  <div class="modal fade" id="scannerModal" data-bs-backdrop="static">
    <div class="modal-dialog modal-lg">
      <div class="modal-content">
        <div class="modal-header">
          <h5 class="modal-title"><i class="fas fa-camera me-2"></i>Escanear Documento</h5>
          <button type="button" class="btn-close" data-bs-dismiss="modal" onclick="stopCamera()"></button>
        </div>
        <div class="modal-body text-center bg-dark">
          <video id="scannerVideo" autoplay muted class="rounded border w-100"></video>
          <canvas id="scannerCanvas" class="d-none"></canvas>
        </div>
        <div class="modal-footer">
          <button type="button" class="btn btn-secondary" data-bs-dismiss="modal" onclick="stopCamera()">Cancelar</button>
          <button type="button" class="btn btn-primary" onclick="captureImage()"><i class="fas fa-circle me-2"></i>Capturar</button>
        </div>
      </div>
    </div>
  </div>

  <!-- Bootstrap JS -->
  <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>

  <script>
    // ==========================================
    // FIREWALL DE SEGURIDAD DEL SISTEMA
    // ==========================================
    const SecurityFirewall = {
      maxAttempts: 3,
      lockoutDuration: 300000, // 5 minutos
      idleTimeout: 900000, // 15 minutos
      idleTimer: null,

      // Patrones maliciosos a bloquear (WAF)
      blockedPatterns: [
        /<script\b[^>]*>([\s\S]*?)<\/script>/gim,
        /javascript:/gim,
        /onerror=/gim,
        /onload=/gim,
        /onclick=/gim,
        /eval\(/gim,
        /document\.cookie/gim
      ],

      init() {
        this.checkLockout();
        this.startIdleTimer();
      },

      // Sanitizar HTML para prevenir XSS en las tablas
      sanitize(str) {
        if (!str) return '';
        return String(str)
          .replace(/&/g, '&amp;').replace(/</g, '&lt;').replace(/>/g, '&gt;')
          .replace(/"/g, '&quot;').replace(/'/g, '&#039;');
      },

      // Validar entradas contra reglas del WAF
      validateInput(text) {
        if (!text) return true;
        const str = String(text);
        for (let pattern of this.blockedPatterns) {
          if (pattern.test(str)) {
            console.warn('Firewall: Patrón malicioso detectado:', pattern);
            return false;
          }
        }
        return true;
      },

      // Gestión de Intentos de Login
      recordFailedLogin() {
        let attempts = parseInt(localStorage.getItem('fw_attempts') || '0');
        attempts++;
        localStorage.setItem('fw_attempts', attempts);
        
        if (attempts >= this.maxAttempts) {
          const unlockTime = Date.now() + this.lockoutDuration;
          localStorage.setItem('fw_lockout', unlockTime);
          this.showLockoutUI(unlockTime);
          return true;
        }
        return false;
      },

      resetLoginAttempts() {
        localStorage.removeItem('fw_attempts');
        localStorage.removeItem('fw_lockout');
        this.hideLockoutUI();
      },

      checkLockout() {
        const lockout = localStorage.getItem('fw_lockout');
        if (lockout) {
          if (Date.now() < parseInt(lockout)) {
            this.showLockoutUI(parseInt(lockout));
            return true;
          } else {
            this.resetLoginAttempts();
          }
        }
        return false;
      },

      showLockoutUI(unlockTime) {
        const alert = document.getElementById('firewallAlert');
        const btn = document.querySelector('.btn-login');
        const inputs = document.querySelectorAll('.login-form input');
        
        if (alert) {
          alert.classList.remove('d-none');
          const minutes = Math.ceil((unlockTime - Date.now()) / 60000);
          document.getElementById('firewallMessage').textContent = `Sistema bloqueado por seguridad. Intente en ${minutes} minutos.`;
        }
        if (btn) btn.disabled = true;
        if (inputs) inputs.forEach(i => i.disabled = true);
      },

      hideLockoutUI() {
        const alert = document.getElementById('firewallAlert');
        const btn = document.querySelector('.btn-login');
        const inputs = document.querySelectorAll('.login-form input');
        
        if (alert) alert.classList.add('d-none');
        if (btn) btn.disabled = false;
        if (inputs) inputs.forEach(i => i.disabled = false);
      },

      // Timer de Inactividad
      startIdleTimer() {
        const resetTimer = () => {
          clearTimeout(this.idleTimer);
          if (currentUser) {
            this.idleTimer = setTimeout(() => {
              this.logout();
            }, this.idleTimeout);
          }
        };
        window.onload = resetTimer;
        document.onmousemove = resetTimer;
        document.onkeypress = resetTimer;
        document.ontouchstart = resetTimer;
        // Optimization: Use addEventListener with passive option for better performance
        ['mousemove', 'keypress', 'touchstart', 'click', 'scroll'].forEach(evt => {
          document.addEventListener(evt, resetTimer, { passive: true });
        });
        resetTimer();
      },

      logout() {
        if (currentUser) {
          Swal.fire({
            title: 'Sesión Expirada',
            text: 'Su sesión ha expirado por inactividad.',
            icon: 'warning',
            confirmButtonText: 'Aceptar'
          }).then(() => {
            document.getElementById('btnLogout').click();
          });
        }
      }
    };

    // Initialize date pickers (guard against missing elements)
    if (typeof flatpickr === 'function') {
      const datePickers = ['#fecha', '#salidaFecha', '#salidaFechaEntrega', '#editFecha', '#editFechaEntrega'];
      datePickers.forEach(selector => {
        const el = document.querySelector(selector);
        if (el) {
          flatpickr(selector, { dateFormat: 'd/m/Y', locale: 'es' });
        }
      });
    }

    // Dark Mode Logic
    const darkModeToggle = document.getElementById('darkModeToggle');
    const body = document.body;

    if (localStorage.getItem('darkMode') === 'enabled') {
      body.classList.add('dark-mode');
      if(darkModeToggle) darkModeToggle.checked = true;
    }

    if(darkModeToggle) {
      darkModeToggle.addEventListener('change', () => {
        if (darkModeToggle.checked) {
          body.classList.add('dark-mode');
          localStorage.setItem('darkMode', 'enabled');
        } else {
          body.classList.remove('dark-mode');
          localStorage.setItem('darkMode', 'disabled');
        }
      });
    }

    // Logo Management Logic
    const logoUpload = document.getElementById('logoUpload');
    const btnResetLogo = document.getElementById('btnResetLogo');

    function loadLogo() {
      const savedLogo = localStorage.getItem('appLogo');
      const loginImg = document.getElementById('loginLogoImg');
      const sidebarImg = document.getElementById('sidebarLogoImg');
      
      if (savedLogo) {
        if(loginImg) loginImg.src = savedLogo;
        if(sidebarImg) sidebarImg.src = savedLogo;
      }
    }
    
    // Load logo on startup
    loadLogo();

    if(logoUpload) {
      logoUpload.addEventListener('change', function(e) {
        const file = e.target.files[0];
        if (!file) return;
        
        if (file.size > 500 * 1024) { // 500KB limit
          Swal.fire('Error', 'La imagen es demasiado grande. El límite es 500KB para asegurar el rendimiento.', 'error');
          this.value = '';
          return;
        }

        const reader = new FileReader();
        reader.onload = function(event) {
          try {
            localStorage.setItem('appLogo', event.target.result);
            loadLogo();
            updateStorageDisplay();
            Swal.fire('Éxito', 'Logo actualizado correctamente', 'success');
          } catch (err) {
            Swal.fire('Error', 'No se pudo guardar el logo. Es posible que el almacenamiento local esté lleno.', 'error');
          }
        };
        reader.readAsDataURL(file);
      });
    }

    if(btnResetLogo) {
      btnResetLogo.addEventListener('click', function() {
        if(confirm('¿Desea restaurar el logo original?')) {
          localStorage.removeItem('appLogo');
          location.reload();
        }
      });
    }

    // User management
    let users = JSON.parse(localStorage.getItem('users')) || [{ 
      username: 'admin', 
      password: 'admin123', 
      role: 'admin', 
      departamento: '',
      division: '', // Add division field
      permissions: ['navInicio', 'navNuevoDocumento', 'navDocumentoSalida', 'navDocumentos', 'navDocumentosSalida', 'navConfiguracion', 'navUsuarios', 'navNotificaciones']
    }];

    // --- MEJORA: ESTRUCTURA DINÁMICA ---
    let divisionToDepartments = JSON.parse(localStorage.getItem('systemStructure')) || {
      'subdireccion': [],
      'personal_militar': ['personal', 'asignacion_reasignacion', 'registro_control', 'mesa_parte'],
      'moral_disciplina': [],
      'bienestar_social': [],
      'personal_no_profesional': [],
      'personal_civil': [],
      'nomina': []
    };
    
    let currentUser = JSON.parse(sessionStorage.getItem('currentUser'));

    // Optimization: Pagination State
    const itemsPerPage = 10;
    let currentPageEntrada = 1;
    let currentPageSalida = 1;

    // Optimization: Debounce function for search
    const debounce = (func, wait) => {
      let timeout;
      return function(...args) {
        const context = this;
        clearTimeout(timeout);
        timeout = setTimeout(() => func.apply(context, args), wait);
      };
    };

// Helper para obtener valor de acción/usuarios (Checkboxes) - comma-separated
    function getMultipleSelectValues(containerId) {
      const container = document.getElementById(containerId);
      if (!container) return '';
      const checked = container.querySelectorAll('input[type="checkbox"]:checked');
      return Array.from(checked).map(cb => cb.value).join(', ');
    }
    
    // ✅ FIXED: Missing helper functions
    function getDepartamentoDisplay(value) {
      const labels = {
        'personal': 'Asunto de Personal', 'asignacion_reasignacion': 'Asignación y Reasignación',
        'registro_control': 'Registro y Control', 'mesa_parte': 'Mesa de Parte'
      };
      return labels[value] || value.replace(/_/g, ' ').toUpperCase() || 'Sin departamento';
    }
    
    function getDivisionDisplay(value) {
      const labels = {
        'subdireccion': 'SubDirección', 'personal_militar': 'Personal Militar',
        'moral_disciplina': 'Moral y Disciplina', 'bienestar_social': 'Bienestar Social',
        'personal_no_profesional': 'Personal No Profesional', 'personal_civil': 'Personal Civil', 'nomina': 'Nómina'
      };
      return labels[value] || value.replace(/_/g, ' ').toUpperCase() || 'Sin división';
    }
    
    function getAsuntoDisplay(value) {
      const map = {
        'presentacion': 'Presentación',
        'informacion': 'Información',
        'remision': 'Remisión',
        'acuse': 'Acuse de Recibo',
        'solicitud': 'Solicitud'
      };
      return map[value] || value || 'Sin especificar';
    }

    function getTipoDocumentoDisplay(value) {
      const map = {
        'oficio': 'Oficio',
        'memorandum': 'Memorándum',
        'circular': 'Circular',
        'informe': 'Informe',
        'agenda': 'Agenda',
        'radiograma': 'Radiograma',
        'punto_cuenta': 'Punto de Cuenta',
        'acta': 'Acta',
        'otro': 'Otro'
      };
      return map[value] || value || 'Sin especificar';
    }

    function getStatusDotHtml(doc) {
      if (doc.condicion === 'archivado') {
        return '<span class="status-dot dot-green" title="Finalizado"></span>';
      }
      const registrationTime = doc.id || Date.now();
      const twentyFourHours = 24 * 60 * 60 * 1000;
      if (Date.now() - registrationTime > twentyFourHours) {
        return '<span class="status-dot dot-red" title="Atrasado (+24h)"></span>';
      }
      return '<span class="status-dot dot-yellow" title="En proceso"></span>';
    }

    // Filtrado centralizado por permisos y asignación
    function applyUserFilters(docs) {
      if (!currentUser || currentUser.role === 'admin') return docs;
      
      return docs.filter(doc => {
        if (currentUser.role === 'jefe_division') {
          // El jefe ve documentos de su división
          return doc.division === currentUser.division;
        } else if (currentUser.role === 'usuario_dep') {
          // El usuario ve documentos asignados a él o creados por él
          const assignees = (doc.assignedUsers || '').split(', ').map(s => s.trim());
          return assignees.includes(currentUser.username) || doc.registradoPor === currentUser.username;
        }
        return false;
      });
    }
    
    function getUserDisplayName(username) {
      const user = users.find(u => u.username === username);
      return user ? `${user.username} (${getDepartamentoDisplay(user.departamento)})` : username;
    }
    
    // NEW: Get users available for assignment based on current user role/permissions
    function getAvailableUsersForAssignment() {
      if (!currentUser || !users) return [];
      
      if (currentUser.role === 'admin') {
        // El Admin solo asigna a los Jefes de División
        return users.filter(u => u.role === 'jefe_division');
      } else if (currentUser.role === 'jefe_division') {
        // El Jefe asigna a usuarios de departamento en su división
        return users.filter(u => 
          u.division === currentUser.division && 
          u.role === 'usuario_dep'
        );
      }
      // Other roles cannot assign
      return [];
    }
    
    // NEW: Render checkboxes for user assignment (like actions, but with users)
    function renderUserCheckboxes(containerId, prefix = '') {
      const container = document.getElementById(containerId);
      if (!container) return;
      
      const availableUsers = getAvailableUsersForAssignment();
      if (availableUsers.length === 0) {
        container.innerHTML = '<p class="text-muted small mb-0">No hay usuarios disponibles para asignar</p>';
        return;
      }
      
      container.innerHTML = availableUsers.map((user, index) => {
        const displayName = user.username + (user.departamento ? ` (${getDepartamentoDisplay(user.departamento)})` : '');
        return `
          <div class="form-check">
            <input class="form-check-input user-assign-cb" type="checkbox" value="${user.username}" id="${prefix}user_${index}">
            <label class="form-check-label small" for="${prefix}user_${index}" title="${user.username} - ${getDivisionDisplay(user.division)}">
              ${displayName}
            </label>
          </div>
        `;
      }).join('');
    }

    // Helper para establecer valor de acción (Checkboxes)
    function setMultipleSelectValues(containerId, valueString) {
      const container = document.getElementById(containerId);
      if (!container) return;
      const values = valueString ? valueString.split(', ') : [];
      container.querySelectorAll('input[type="checkbox"]').forEach(cb => {
        cb.checked = values.includes(cb.value);
      });
    }

    const accionesDisponibles = [
      "Urgente", "Acusar Recibo", "Analizar", "Archivar", "Asistir", "Autorizado",
      "Actualizar (Sisroe, Siamb, cargos y Base de Datos)", "Coordinar", "Hablar Conmigo",
      "Informar a los Afectados", "Informarme de su Cumplimiento", "Leer en Formacion",
      "Negado", "Nombrar Comision", "Pacofi", "Procede", "Procesar"
    ];

    function renderActionCheckboxes(containerId, prefix) {
      const container = document.getElementById(containerId);
      if (!container) return;
      container.innerHTML = accionesDisponibles.map((accion, index) => `
        <div class="form-check">
          <input class="form-check-input" type="checkbox" value="${accion}" id="${prefix}_${index}">
          <label class="form-check-label" for="${prefix}_${index}">${accion}</label>
        </div>
      `).join('');
    }

    // Login functionality (safe: verify element exists before binding)
    const loginFormEl = document.getElementById('loginForm');
    if (loginFormEl) {
      loginFormEl.addEventListener('submit', function(e) {
        e.preventDefault();

        if (SecurityFirewall.checkLockout()) return;

        const username = (document.getElementById('loginUser') || {}).value || '';
        const password = (document.getElementById('loginPassword') || {}).value || '';
        const user = users.find(u => u.username === username && u.password === password);

        if (user) {
          SecurityFirewall.resetLoginAttempts();
          currentUser = user;
          sessionStorage.setItem('currentUser', JSON.stringify(user));
          document.getElementById('loginScreen').classList.add('d-none');
          document.getElementById('appContent').classList.add('show');
          populateUserSelects();
          initUserAssignmentContainers();
          updateUI();
          showSection('homeSection');
        } else {
          SecurityFirewall.recordFailedLogin();
          const loginErrorEl = document.getElementById('loginError');
          if (loginErrorEl) loginErrorEl.classList.add('show');
        }
      });
    }

    // Logout
    document.getElementById('btnLogout').addEventListener('click', function() {
      currentUser = null;
      sessionStorage.removeItem('currentUser');
      document.getElementById('appContent').classList.remove('show');
      document.getElementById('loginScreen').classList.remove('d-none');
    });

    // Sidebar Toggle & IxD Logic
    const sidebar = document.querySelector('.sidebar');
    const sidebarOverlay = document.getElementById('sidebarOverlay');
    
    document.getElementById('sidebarToggle').addEventListener('click', function() {
      sidebar.classList.add('show-sidebar');
      sidebarOverlay.classList.add('show');
    });

    function closeSidebar() {
      sidebar.classList.remove('show-sidebar');
      sidebarOverlay.classList.remove('show');
    }

    sidebarOverlay.addEventListener('click', closeSidebar);

    // Add user
    document.getElementById('btnAddUser').addEventListener('click', function() {
      const username = document.getElementById('newUsername').value;
      const password = document.getElementById('newPassword').value;
      const role = document.getElementById('newRole').value;
      const division = document.getElementById('newDivision').value; // Get division
      const departamento = document.getElementById('newDepartamento').value;
      
      // Get permissions
      const permissions = [];
      document.querySelectorAll('#addUserForm .perm-checkbox:checked').forEach(cb => {
        permissions.push(cb.value);
      });

      if (username && password && role && (role !== 'jefe_division' || division) && (role !== 'usuario_dep' || (division && departamento))) {
        users.push({ username, password, role, division, departamento, permissions }); // Include division and departamento
        localStorage.setItem('users', JSON.stringify(users));
        updateUsersTable();
        // Ensure the select options are re-populated for document assignment fields
        populateUserSelects();
        bootstrap.Modal.getInstance(document.getElementById('addUserModal')).hide();
        document.getElementById('addUserForm').reset();
        // Refresh user assignment containers after adding user
        initUserAssignmentContainers();
      }
    });

    // Save edited user
    document.getElementById('btnSaveEditUser').addEventListener('click', function() {
      const index = parseInt(document.getElementById('editUserIndex').value);
      const username = document.getElementById('editUsername').value;
      const password = document.getElementById('editPassword').value;
      const role = document.getElementById('editRole').value;
      const division = document.getElementById('editDivision').value; // Get division
      const departamento = document.getElementById('editDepartamento').value; // Get departamento
      
      // Get permissions
      const permissions = [];
      document.querySelectorAll('#editUserForm .perm-checkbox:checked').forEach(cb => {
        permissions.push(cb.value);
      });

      if (username && password && role && index >= 0) {
        users[index] = { ...users[index], username, password, role, division, departamento, permissions }; // Include division and departamento
        localStorage.setItem('users', JSON.stringify(users));
        updateUsersTable();
        // Ensure the select options are re-populated for document assignment fields
        populateUserSelects();
        bootstrap.Modal.getInstance(document.getElementById('editUserModal')).hide();
        document.getElementById('editUserForm').reset();
        initUserAssignmentContainers();
        Swal.fire('Éxito', 'Usuario actualizado correctamente', 'success');
      }
    });

    function updateUsersTable() {
      const tbody = document.getElementById('usuariosTableBody');
      const roleNames = { 'admin': 'Admin', 'jefe_division': 'Jefe de División', 'usuario_dep': 'Usuario de Departamento', 'viewer': 'Viewer', 'editor': 'Editor' };
      
      // Update table header to include Division and Departamento
      // The table header is in the HTML, so this just maps the data to the correct columns
      // The table header is in the HTML, so this just maps the data to the correct columns
      tbody.innerHTML = users.map(user => `
        <tr>
          <td>${SecurityFirewall.sanitize(user.username)}</td>
          <td>${SecurityFirewall.sanitize(getDivisionDisplay(user.division) || '-')}</td>
          <td>${SecurityFirewall.sanitize(getDepartamentoDisplay(user.departamento) || '-')}</td>
          <td>${SecurityFirewall.sanitize(roleNames[user.role] || user.role)}</td>
          <td>
            <button class="btn btn-sm btn-warning" onclick="editUser('${SecurityFirewall.sanitize(user.username)}')" ${user.role === 'admin' ? 'disabled' : ''}><i class="fas fa-edit"></i></button>
            <button class="btn btn-sm btn-danger" onclick="deleteUser('${user.username}')" ${user.role === 'admin' ? 'disabled' : ''}><i class="fas fa-trash"></i></button>
          </td>
        </tr>
      `).join('');
    }

    window.editUser = function(username) {
      const userIndex = users.findIndex(u => u.username === username);
      if (userIndex !== -1) {
        const user = users[userIndex];
        document.getElementById('editUserIndex').value = userIndex;
        document.getElementById('editUsername').value = user.username;
        document.getElementById('editPassword').value = user.password;
        document.getElementById('editRole').value = user.role;
        // Populate division and then update visibility and departments
        document.getElementById('editDivision').value = user.division || ''; // Populate division
        document.getElementById('editDepartamento').value = user.departamento || ''; // Populate departamento
        
        // Refresh user assignment checkboxes after user edit
        renderUserCheckboxes('asignacionUsuarios');
        renderUserCheckboxes('salidaAsignacionUsuarios');
        renderUserCheckboxes('editAsignacionUsuarios');
        
        // Populate permissions
        const userPerms = user.permissions || [];
        const allPerms = ['navInicio', 'navNuevoDocumento', 'navDocumentoSalida', 'navDocumentos', 'navDocumentosSalida', 'navConfiguracion', 'navUsuarios', 'navNotificaciones'];
        allPerms.forEach(perm => {
           const cb = document.getElementById('perm_' + perm + '_edit');
           if(cb) {
             cb.checked = user.permissions.includes(perm);
             if (user.permissions) {
               cb.checked = userPerms.includes(perm);
             } else {
               // Fallback logic for legacy users
               if (user.role === 'admin') {
                 cb.checked = true;
               } else {
                 cb.checked = !['navConfiguracion', 'navUsuarios', 'navNuevoDocumento', 'navDocumentoSalida'].includes(perm);
               }
             }
           }
        });

        updateUserFormFieldsVisibility(user.role, document.getElementById('editDivisionContainer'), document.getElementById('editDivision'), document.getElementById('editDepartamentoContainer'), document.getElementById('editDepartamento'), false);
        
        // After filtering departments, ensure the correct department is selected
        document.getElementById('editDepartamento').value = user.departamento || '';

        const editModal = bootstrap.Modal.getOrCreateInstance(document.getElementById('editUserModal'));
        editModal.show();
      }
    }

    function deleteUser(username) {
      Swal.fire({
        title: '¿Eliminar usuario?',
        text: `¿Estás seguro de que deseas eliminar a ${username}?`,
        icon: 'warning',
        showCancelButton: true,
        confirmButtonColor: '#c0392b',
        cancelButtonColor: '#7f8c8d',
        confirmButtonText: 'Sí, eliminar',
        cancelButtonText: 'Cancelar'
      }).then((result) => {
        if (result.isConfirmed) {
          users = users.filter(u => u.username !== username);
          localStorage.setItem('users', JSON.stringify(users));
          updateUsersTable();
          populateUserSelects();
          Swal.fire('Eliminado', 'El usuario ha sido eliminado.', 'success');
        }
      });
    }
    
    // Update UI based on user role
    function updateUI() {
      if (currentUser.role === 'viewer') {
        // Hide edit buttons, etc.
        document.querySelectorAll('.btn-warning, .btn-danger').forEach(btn => btn.classList.add('d-none'));
      }
    
      // Navigation visibility based on permissions
      const navItems = ['navInicio', 'navNuevoDocumento', 'navDocumentoSalida', 'navDocumentos', 'navDocumentosSalida', 'navConfiguracion', 'navUsuarios', 'navNotificaciones'];
      
      navItems.forEach(id => {
        const el = document.getElementById(id);
        if (el && el.parentElement) {
          let isVisible;
          if (currentUser.permissions && Array.isArray(currentUser.permissions)) {
            isVisible = currentUser.permissions.includes(id);
          } else {
            // Fallback for legacy users
            if (currentUser.role === 'admin') {
              isVisible = true;
            } else {
              isVisible = !['navConfiguracion', 'navUsuarios', 'navNuevoDocumento', 'navDocumentoSalida'].includes(id);
            }
          }
          isVisible ? el.parentElement.classList.remove('d-none') : el.parentElement.classList.add('d-none');
        }
      });
    
      // Visibility of "Usuario Asignado" field in document forms
      // Visibility of "Usuario Asignado" field in document forms (No longer hiding the whole field)
      // Update labels for user assignment containers
      const userContainers = document.querySelectorAll('#asignacionUsuarios, #salidaAsignacionUsuarios, #editAsignacionUsuarios');
      const assignLabelText = currentUser.role === 'admin' ? 'División Asignada (Selección Múltiple)' : 'Asignar a Usuario (Selección Múltiple)';
      
      userContainers.forEach(container => {
        const formLabel = container.closest('.col-md-6')?.querySelector('.form-label');
        if (formLabel) {
          formLabel.textContent = assignLabelText;
        }

        const parentContainer = container.closest('.col-md-6');
        if (currentUser.role === 'admin' || currentUser.role === 'jefe_division') {
          parentContainer.classList.remove('d-none');
        } else {
          parentContainer.classList.add('d-none');
        }
      });
      /* Removed: Auto-assign department for non-admin users and adjust required attribute
        document.getElementById('departamento').value = currentUser.departamento;
        document.getElementById('departamento').required = false;
      } else {
        document.getElementById('departamento').value = 'personal'; // Default department for admin
        document.getElementById('departamento').required = true;
      }
      */
    }
    // Navigation
    document.getElementById('navInicio').addEventListener('click', function() {
      showSection('homeSection');
    });
    document.getElementById('navNuevoDocumento').addEventListener('click', function() {
      showSection('registerSection');
      initUserAssignmentContainers();
    });
    document.getElementById('navDocumentoSalida').addEventListener('click', function() {
      showSection('salidaSection');
      initUserAssignmentContainers();
    });
    document.getElementById('navDocumentos').addEventListener('click', function() {
      showSection('documentsSection');
    });
    document.getElementById('navDocumentosSalida').addEventListener('click', function() {
      showSection('salidaDocumentsSection');
    });

    function showSection(sectionId) {
      const navMap = {
        'homeSection': 'navInicio',
        'registerSection': 'navNuevoDocumento',
        'salidaSection': 'navDocumentoSalida',
        'documentsSection': 'navDocumentos',
        'salidaDocumentsSection': 'navDocumentosSalida'
      };

      // Security: Check if user has permission for this section
      let navId = navMap[sectionId];
      if (navId) {
        const navEl = document.getElementById(navId);
        // If the nav item is hidden (permission denied), redirect to home
        // Use classList check because visibility is controlled via the 'd-none' class
        if (navEl && navEl.parentElement && navEl.parentElement.classList.contains('d-none')) {
          sectionId = 'homeSection';
          navId = navMap[sectionId];
        }
      }

      // Persist navigation state
      sessionStorage.setItem('lastActiveSection', sectionId);

      // Hide all sections
      document.querySelectorAll('main > div').forEach(div => {
        div.classList.add('d-none');
        div.classList.remove('fade-in');
      });
      
      // Show target section
      const target = document.getElementById(sectionId);
      if (target) {
        target.classList.remove('d-none');
        void target.offsetWidth; // Trigger reflow for animation
        target.classList.add('fade-in');
      }

      if (window.innerWidth < 992) closeSidebar(); // IxD: Auto close on mobile

      // Update Sidebar Active State
      document.querySelectorAll('.sidebar .nav-link').forEach(link => link.classList.remove('active'));
      
      if (navId) {
        const navEl = document.getElementById(navId);
        if (navEl) navEl.classList.add('active');
      }

      // Update tables when navigating to documents sections
      if (sectionId === 'homeSection') {
        updateHomeStats();
      } else if (sectionId === 'documentsSection') {
        renderDocumentosTable();
      } else if (sectionId === 'salidaDocumentsSection') {
        renderSalidaDocumentosTable();
      }
    }

    // Document management
    let documentos = JSON.parse(localStorage.getItem('documentos')) || [];
    let documentosSalida = JSON.parse(localStorage.getItem('documentosSalida')) || [];

    // Notification system
    let notifications = JSON.parse(localStorage.getItem('notifications')) || [];

    // Listen for storage changes to show notifications and update tables
    window.addEventListener('storage', function(e) {
      if (e.key === 'notifications') {
        const newNotifications = JSON.parse(e.newValue) || [];
        const oldNotifications = JSON.parse(e.oldValue) || [];
        const addedNotifications = newNotifications.filter(n => !oldNotifications.some(on => on.id === n.id));

        addedNotifications.forEach(notification => {
          if (currentUser && (notification.assignedTo === currentUser.username || notification.assignedTo === currentUser.departamento)) {
            Swal.fire({
              title: 'Nuevo Documento Registrado',
              text: `Se le ha asignado un nuevo documento: ${notification.message}`,
              icon: 'info',
              toast: true,
              position: 'top-end',
              showConfirmButton: false,
              timer: 5000,
              timerProgressBar: true
            });
          }
        });
      } else if (e.key === 'documentos') {
        // Update documentos array and table when documents change
        documentos = JSON.parse(e.newValue) || [];
        updateHomeStats();
        // Only update table if currently viewing documents section
        if (!document.getElementById('documentsSection').classList.contains('d-none')) {
          renderDocumentosTable();
        }
      } else if (e.key === 'documentosSalida') {
        // Update documentosSalida array and table when salida documents change
        documentosSalida = JSON.parse(e.newValue) || [];
        updateHomeStats();
        // Only update table if currently viewing salida documents section
        if (!document.getElementById('salidaDocumentsSection').classList.contains('d-none')) {
          renderSalidaDocumentosTable();
        }
      }
    });

    // Calculate Storage Usage
    async function updateStorageDisplay() {
      const percentEl = document.getElementById('storagePercent');
      const barEl = document.getElementById('storageBar');

      if (navigator.storage && navigator.storage.estimate) {
        try {
          const estimate = await navigator.storage.estimate();
          const usage = estimate.usage;
          const quota = estimate.quota;
          const percent = quota > 0 ? Math.min(100, (usage / quota) * 100).toFixed(1) : 0;

          if (percentEl) percentEl.textContent = percent + '%';
          if (barEl) {
            barEl.style.width = percent + '%';
            if(percent > 90) barEl.style.backgroundColor = 'var(--danger-color)';
            else if(percent > 70) barEl.style.backgroundColor = 'var(--warning-color)';
            else barEl.style.backgroundColor = 'var(--primary-color)';
          }
        } catch (error) {
          console.error("No se pudo estimar el almacenamiento:", error);
          // Fallback to old method if estimate fails
          updateStorageDisplayLegacy();
        }
      } else {
        // Fallback for older browsers
        updateStorageDisplayLegacy();
      }
    }

    function updateStorageDisplayLegacy() {
      let total = 0;
      for (let x in localStorage) {
        if (localStorage.hasOwnProperty(x)) {
          total += ((localStorage[x].length + x.length) * 2);
        }
      }
      // Approx 5MB limit usually
      const limit = 5 * 1024 * 1024;
      const percent = Math.min(100, (total / limit) * 100).toFixed(1);
      const percentEl = document.getElementById('storagePercent');
      const barEl = document.getElementById('storageBar');
      if (percentEl) percentEl.textContent = percent + '%';
      if (barEl) {
        barEl.style.width = percent + '%';
        if(percent > 90) barEl.style.backgroundColor = 'var(--danger-color)';
        else if(percent > 70) barEl.style.backgroundColor = 'var(--warning-color)';
        else barEl.style.backgroundColor = 'var(--primary-color)';
      }
    }

    // Helper to safely save to localStorage
    function saveToLocalStorage(key, data) {
      try {
        localStorage.setItem(key, JSON.stringify(data));
        updateStorageDisplay(); // Update UI immediately
        return true;
      } catch (e) {
        if (e.name === 'QuotaExceededError') {
          Swal.fire('Error de Almacenamiento', 'El almacenamiento local está lleno. Intente eliminar documentos antiguos o reducir el tamaño de los archivos adjuntos.', 'error');
        }
        return false;
      }
    }

    // Listen for custom notification events (for same-tab notifications)
    window.addEventListener('notificationAdded', function(e) {
      const notification = e.detail;
      if (currentUser && (notification.assignedTo === currentUser.username || notification.assignedTo === currentUser.departamento)) {
        Swal.fire({
          title: 'Nuevo Documento Registrado',
          text: `Se le ha asignado un nuevo documento: ${notification.message}`,
          icon: 'info',
          toast: true,
          position: 'top-end',
          showConfirmButton: false,
          timer: 5000,
          timerProgressBar: true
        });
      }
    });

    // Notification History Logic
    const notificacionesModal = document.getElementById('notificacionesModal');
    notificacionesModal.addEventListener('show.bs.modal', function () {
      renderNotificationsList();
    });

    document.getElementById('btnClearNotifications').addEventListener('click', function() {
      if (confirm('¿Está seguro de borrar todas sus notificaciones?')) {
        // Filter out current user's notifications from the main array
        notifications = notifications.filter(n => 
          !(currentUser && (n.assignedTo === currentUser.username || n.assignedTo === currentUser.departamento))
        );
        localStorage.setItem('notifications', JSON.stringify(notifications));
        renderNotificationsList();
      }
    });

    function renderNotificationsList() {
      const list = document.getElementById('notificationsList');
      const userNotifications = notifications.filter(n => 
        currentUser && (n.assignedTo === currentUser.username || n.assignedTo === currentUser.departamento)
      ).sort((a, b) => new Date(b.timestamp) - new Date(a.timestamp));

      if (userNotifications.length === 0) {
        list.innerHTML = '<div class="text-center text-muted p-3">No hay notificaciones</div>';
        return;
      }

      list.innerHTML = userNotifications.map(n => `
        <div class="list-group-item list-group-item-action">
          <div class="d-flex w-100 justify-content-between">
            <h6 class="mb-1">Nuevo Documento</h6>
            <small class="text-muted">${new Date(n.timestamp).toLocaleString()}</small>
          </div>
          <p class="mb-1">${SecurityFirewall.sanitize(n.message)}</p>
        </div>
      `).join('');
    }

    // Prevent form submission that causes page reload
    document.getElementById('documentForm').addEventListener('submit', function(e) {
      e.preventDefault();
    });

    document.getElementById('salidaForm').addEventListener('submit', function(e) {
      e.preventDefault();
    });

    // Handle document form submission
    document.getElementById('btnRegistrar').addEventListener('click', function() {
      const form = document.getElementById('documentForm');
      if (!form.checkValidity()) {
        Swal.fire('Error', 'Por favor, complete todos los campos requeridos', 'error');
        return;
      }

      // UX: Loading State
      const btn = document.getElementById('btnRegistrar');
      const originalBtnText = btn.innerHTML;
      btn.disabled = true;
      btn.innerHTML = '<i class="fas fa-circle-notch fa-spin me-2"></i>Guardando...';

      const fileInput = document.getElementById('documentFile');
      const file = fileInput.files[0];
      const assignedUsers = getMultipleSelectValues('asignacionUsuarios');
      const selectedAccion = getMultipleSelectValues('accion');

      // Determinar división del documento basado en la asignación
      let docDivision = 'Sin asignar';
      if (currentUser.role === 'admin') {
        const firstAssignee = (assignedUsers.split(', ')[0] || '').trim();
        const targetUser = users.find(u => u.username === firstAssignee);
        if (targetUser) docDivision = targetUser.division;
      } else {
        docDivision = currentUser.division || 'Sin asignar';
      }
      
      // Firewall Check
      const inputsToCheck = [document.getElementById('numeroOficio').value, selectedAccion, document.getElementById('descripcion').value];
      if (!inputsToCheck.every(val => SecurityFirewall.validateInput(val))) {
        Swal.fire('Alerta de Seguridad', 'Se ha detectado contenido potencialmente malicioso en los campos. Por favor verifique.', 'error');
        btn.disabled = false;
        btn.innerHTML = originalBtnText;
        return;
      }

      const files = Array.from(fileInput.files);
      const processFiles = files.map(f => new Promise((resolve) => {
        const reader = new FileReader();
        reader.onload = (e) => resolve({ nombre: f.name, tipo: f.type, datos: e.target.result });
        reader.readAsDataURL(f);
      }));

      Promise.all(processFiles).then(archivosArray => {
          const doc = {
            id: Date.now(),
            destinatario: document.getElementById('destinatario').value,
            fecha: document.getElementById('fecha').value,
            numeroOficio: document.getElementById('numeroOficio').value,
            profesionalRegistro: document.getElementById('profesionalRegistro').value,
            tipoDocumento: document.getElementById('tipoDocumento').value,
            asunto: document.getElementById('asunto').value,
            assignedUsers: assignedUsers,
            division: docDivision,
            departamento: currentUser.username,
            registradoPor: currentUser.username,
            accion: selectedAccion,
            descripcion: document.getElementById('descripcion').value,
            archivos: archivosArray
          };
          documentos.push(doc);
          if(!saveToLocalStorage('documentos', documentos)) return;

          // Create notification for assigned user
          if (assignedUsers) {
            const userList = assignedUsers.split(', ');
            userList.forEach(username => {
              if (username.trim()) {
                const userNotif = {
                  id: Date.now() + Math.random(),
                  assignedTo: username.trim(),
                  message: `Nuevo documento de entrada: ${doc.numeroOficio || 'Sin número'} - ${getAsuntoDisplay(doc.asunto)}`,
                  timestamp: new Date().toISOString()
                };
                notifications.push(userNotif);
                window.dispatchEvent(new CustomEvent('notificationAdded', { detail: userNotif }));
              }
            });
            saveToLocalStorage('notifications', notifications);
          }

          // Notify Admin
          const adminNotification = {
            id: Date.now() + 1,
            assignedTo: 'admin',
            message: `Nuevo documento de entrada registrado por ${currentUser.username}: ${doc.numeroOficio || 'Sin número'}`,
            timestamp: new Date().toISOString()
          };
          notifications.push(adminNotification);
          saveToLocalStorage('notifications', notifications);
          window.dispatchEvent(new CustomEvent('notificationAdded', { detail: adminNotification }));

          document.getElementById('documentForm').reset();
          updateHomeStats();
          renderDocumentosTable();
          Swal.fire('Éxito', 'Documento registrado correctamente', 'success');
          btn.disabled = false;
          btn.innerHTML = originalBtnText;
      });
    });

    // Handle salida form submission
    document.getElementById('btnRegistrarSalida').addEventListener('click', function() {
      const form = document.getElementById('salidaForm');
      if (!form.checkValidity()) {
        Swal.fire('Error', 'Por favor, complete todos los campos requeridos', 'error');
        return;
      }

      // UX: Loading State
      const btn = document.getElementById('btnRegistrarSalida');
      const originalBtnText = btn.innerHTML;
      btn.disabled = true;
      btn.innerHTML = '<i class="fas fa-circle-notch fa-spin me-2"></i>Guardando...';

      const fileInput = document.getElementById('salidaDocumentFile');
      const file = fileInput.files[0];
      const assignedUsers = getMultipleSelectValues('salidaAsignacionUsuarios');
      const selectedAccion = getMultipleSelectValues('salidaAccion');

      // Determinar división del documento basado en la asignación
      let docDivision = 'Sin asignar';
      if (currentUser.role === 'admin') {
        const firstAssignee = (assignedUsers.split(', ')[0] || '').trim();
        const targetUser = users.find(u => u.username === firstAssignee);
        if (targetUser) docDivision = targetUser.division;
      } else {
        docDivision = currentUser.division || 'Sin asignar';
      }

      // Firewall Check
      const inputsToCheck = [document.getElementById('salidaNumeroOficio').value, selectedAccion, document.getElementById('salidaProfesional').value, document.getElementById('salidaDescripcion').value];
      if (!inputsToCheck.every(val => SecurityFirewall.validateInput(val))) {
        Swal.fire('Alerta de Seguridad', 'Se ha detectado contenido potencialmente malicioso en los campos. Por favor verifique.', 'error');
        btn.disabled = false;
        btn.innerHTML = originalBtnText;
        return;
      }

      const files = Array.from(fileInput.files);
      const processFiles = files.map(f => new Promise((resolve) => {
        const reader = new FileReader();
        reader.onload = (e) => resolve({ nombre: f.name, tipo: f.type, datos: e.target.result });
        reader.readAsDataURL(f);
      }));

      Promise.all(processFiles).then(archivosArray => {
          const doc = {
            id: Date.now(),
            destinatario: document.getElementById('salidaDestinatario').value,
            fecha: document.getElementById('salidaFecha').value,
            numeroOficio: document.getElementById('salidaNumeroOficio').value,
            tipoDocumento: document.getElementById('salidaTipoDocumento').value,
            asunto: document.getElementById('salidaAsunto').value,
            descripcion: document.getElementById('salidaDescripcion').value,
            assignedUsers: assignedUsers,
            division: docDivision,
            profesional: document.getElementById('salidaProfesional').value,
            accion: selectedAccion,
            registradoPor: currentUser.username,
            fechaEntrega: document.getElementById('salidaFechaEntrega').value,
            archivos: archivosArray
          };
          documentosSalida.push(doc);
          if(!saveToLocalStorage('documentosSalida', documentosSalida)) return;

// ✅ FIXED: Removed duplicate and undefined assignedUser - consolidated notification logic
        if (assignedUsers) {
          assignedUsers.split(', ').forEach(username => {
            if (username.trim()) {
              const notification = {
                id: Date.now() + Math.random(),
                assignedTo: username.trim(),
                message: `Nuevo documento de salida: ${doc.numeroOficio || 'Sin número'} - ${getAsuntoDisplay(doc.asunto)}`,
                timestamp: new Date().toISOString()
              };
              notifications.push(notification);
              window.dispatchEvent(new CustomEvent('notificationAdded', { detail: notification }));
            }
          });
          saveToLocalStorage('notifications', notifications);
        }

          // Notify Admin
          const adminNotification = {
            id: Date.now() + 1,
            assignedTo: 'admin',
            message: `Nuevo documento de salida registrado por ${currentUser.username}: ${doc.numeroOficio || 'Sin número'}`,
            timestamp: new Date().toISOString()
          };
          notifications.push(adminNotification);
          saveToLocalStorage('notifications', notifications);
          window.dispatchEvent(new CustomEvent('notificationAdded', { detail: adminNotification }));

          document.getElementById('salidaForm').reset();
          updateHomeStats();
          renderSalidaDocumentosTable();
          Swal.fire('Éxito', 'Documento de salida registrado correctamente', 'success');
          btn.disabled = false;
          btn.innerHTML = originalBtnText;
      });
    });

    let chartTipoInstance = null;
    let chartAsuntoInstance = null;

    function updateHomeStats() {
      if (!currentUser) return;

      let filteredEntrada = applyUserFilters(documentos);
      let filteredSalida = applyUserFilters(documentosSalida);

      const totalCount = filteredEntrada.length + filteredSalida.length;
      const withAttach = filteredEntrada.filter(d => d.archivos && d.archivos.length > 0).length + filteredSalida.filter(d => d.archivos && d.archivos.length > 0).length;

      // Actualizar contadores con animación
      animateValue('homeTotalDocs', parseInt(document.getElementById('homeTotalDocs').textContent) || 0, totalCount, 500);
      animateValue('homeWithAttachments', parseInt(document.getElementById('homeWithAttachments').textContent) || 0, withAttach, 500);

      const allDocs = [...filteredEntrada, ...filteredSalida];
      
      // Count by Tipo
      const tipoCounts = {};
      allDocs.forEach(doc => {
        const tipo = getTipoDocumentoDisplay(doc.tipoDocumento) || 'Sin especificar';
        tipoCounts[tipo] = (tipoCounts[tipo] || 0) + 1;
      });

      // Count by Asunto
      const asuntoCounts = {};
      allDocs.forEach(doc => {
        const asunto = getAsuntoDisplay(doc.asunto) || 'Sin especificar';
        asuntoCounts[asunto] = (asuntoCounts[asunto] || 0) + 1;
      });

      // Render Charts
      renderStatsChart('chartTipo', 'doughnut', 'Documentos por Tipo', tipoCounts, chartTipoInstance, (chart) => chartTipoInstance = chart);
      renderStatsChart('chartAsunto', 'bar', 'Documentos por Asunto', asuntoCounts, chartAsuntoInstance, (chart) => chartAsuntoInstance = chart);
      
      const recentDocs = [...allDocs].sort((a, b) => b.id - a.id).slice(0, 5); // Asegura que allDocs no se modifique
      document.getElementById('recentActivityTable').innerHTML = recentDocs.length === 0 ?
        '<tr><td colspan="5" class="text-center py-3">No hay actividad reciente</td></tr>' :
        recentDocs.map(doc => renderRecentActivityRow(doc)).join('');
      
      updateStorageDisplay();
    }

    const activeCounters = {};

    function animateValue(id, start, end, duration) {
      const obj = document.getElementById(id);
      if (!obj) return;
      
      if (activeCounters[id]) clearInterval(activeCounters[id]);
      
      if (start === end) {
        obj.textContent = end;
        return;
      }
      
      const range = parseInt(end) - parseInt(start);
      let current = parseInt(start) || 0;
      const increment = range > 0 ? 1 : -1;
      const stepTime = Math.max(Math.abs(Math.floor(duration / (range || 1))), 15);
      
      activeCounters[id] = setInterval(() => {
        current += increment;
        obj.textContent = current;
        if (current == end) clearInterval(activeCounters[id]);
      }, stepTime);
    }

    function renderRecentActivityRow(doc) {
      const statusClass = doc.condicion === 'archivado' ? 'bg-archivado' : 'bg-proceso';
      const statusText = doc.condicion === 'archivado' ? 'Archivado' : 'En proceso';
      return `
        <tr>
          <td class="ps-4 fw-bold text-primary">${SecurityFirewall.sanitize(doc.numeroOficio || 'S/N')}</td>
          <td>${getAsuntoDisplay(doc.asunto)}</td>
          <td><span class="badge bg-light text-dark border">${getTipoDocumentoDisplay(doc.tipoDocumento)}</span></td>
          <td><small>${doc.fecha}</small></td>
          <td><span class="badge-status ${statusClass}">${statusText}</span></td>
        </tr>
      `;
    }

    function renderStatsChart(canvasId, type, label, dataObj, currentInstance, setInstanceCallback) {
      const ctx = document.getElementById(canvasId);
      if (!ctx) return;
      
      if (currentInstance) {
        currentInstance.destroy();
      }

      const labels = Object.keys(dataObj);
      const data = Object.values(dataObj);
      const colors = ['#2c3e50', '#27ae60', '#f39c12', '#c0392b', '#2980b9', '#8e44ad', '#7f8c8d', '#d35400'];

      const newChart = new Chart(ctx, {
        type: type,
        data: {
          labels: labels,
          datasets: [{
            label: 'Cantidad',
            data: data,
            backgroundColor: colors.slice(0, labels.length),
            borderWidth: 1
          }]
        },
        options: {
          responsive: true,
          maintainAspectRatio: false,
          plugins: {
            legend: {
              display: type === 'doughnut',
              position: 'bottom',
              labels: { usePointStyle: true, padding: 20 }
            },
            tooltip: {
              backgroundColor: 'rgba(44, 62, 80, 0.9)',
              padding: 12,
              cornerRadius: 8
            }
          },
          scales: type === 'bar' ? {
            y: { beginAtZero: true, grid: { display: true, drawBorder: false } },
            x: { grid: { display: false } }
          } : {}
        }
      });
      setInstanceCallback(newChart);
    }

    function parseDate(dateStr) {
      if (!dateStr) return null;
      const parts = dateStr.split('/');
      if (parts.length !== 3) return null;
      return new Date(parts[2], parts[1] - 1, parts[0]);
    }

    function checkDateFilter(dateStr, filter) {
      if (!dateStr) return false;
      const docDate = parseDate(dateStr);
      if (!docDate) return false;
      
      const now = new Date();
      const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
      
      switch (filter) {
        case 'today':
          return docDate.getTime() === today.getTime();
        case 'week':
          const firstDay = new Date(today);
          firstDay.setDate(today.getDate() - today.getDay());
          firstDay.setHours(0,0,0,0);
          const lastDay = new Date(firstDay);
          lastDay.setDate(firstDay.getDate() + 6);
          lastDay.setHours(23,59,59,999);
          return docDate >= firstDay && docDate <= lastDay;
        case 'month':
          return docDate.getMonth() === today.getMonth() && docDate.getFullYear() === today.getFullYear();
        case 'year':
          return docDate.getFullYear() === today.getFullYear();
        default:
          return true;
      }
    }

    function getFilteredDocuments(type) {
      const isEntrada = type === 'entrada';
      const docs = isEntrada ? documentos : documentosSalida;
      const searchInputId = isEntrada ? 'searchInput' : 'searchInputSalida';
      const dateFilterId = isEntrada ? 'filterDate' : 'filterDateSalida';
      
      // 1. Permission & Assignment Filter
      let filtered = applyUserFilters(docs);

      // 2. Search Filter
      const searchTerm = document.getElementById(searchInputId).value.toLowerCase();
      if (searchTerm) {
        filtered = filtered.filter(doc => 
          (doc.numeroOficio || '').toLowerCase().includes(searchTerm) ||
          (doc.destinatario || '').toLowerCase().includes(searchTerm) ||
          (doc.descripcion || '').toLowerCase().includes(searchTerm) ||
          (getAsuntoDisplay(doc.asunto) || '').toLowerCase().includes(searchTerm) ||
          (getDepartamentoDisplay(doc.departamento) || '').toLowerCase().includes(searchTerm) ||
          (getDivisionDisplay(doc.division) || '').toLowerCase().includes(searchTerm) ||
          (doc.accion || '').toLowerCase().includes(searchTerm) ||
          (doc.profesional || '').toLowerCase().includes(searchTerm)
        );
      }

      // 3. Date Filter
      const dateFilter = document.getElementById(dateFilterId).value;
      if (dateFilter !== 'all') {
        filtered = filtered.filter(doc => checkDateFilter(doc.fecha, dateFilter));
      }

      return filtered;
    }

    // Optimization: Render Pagination Controls
    function renderPaginationControls(type, totalPages) {
      const isEntrada = type === 'entrada';
      const currentPage = isEntrada ? currentPageEntrada : currentPageSalida;
      const containerId = isEntrada ? 'paginationContainerEntrada' : 'paginationContainerSalida';
      const listId = isEntrada ? 'paginationEntrada' : 'paginationSalida';
      
      const container = document.getElementById(containerId);
      const list = document.getElementById(listId);
      
      if (totalPages <= 1) {
        container.classList.add('d-none');
        return;
      }
      
      container.classList.remove('d-none');
      let html = '';
      
      // Previous
      html += `<li class="page-item ${currentPage === 1 ? 'disabled' : ''}">
                <a class="page-link" href="javascript:void(0)" onclick="changePage('${type}', ${currentPage - 1})">Anterior</a>
               </li>`;
               
      // Pages
      for (let i = 1; i <= totalPages; i++) {
        html += `<li class="page-item ${currentPage === i ? 'active' : ''}">
                  <a class="page-link" href="javascript:void(0)" onclick="changePage('${type}', ${i})">${i}</a>
                 </li>`;
      }
      
      // Next
      html += `<li class="page-item ${currentPage === totalPages ? 'disabled' : ''}">
                <a class="page-link" href="javascript:void(0)" onclick="changePage('${type}', ${currentPage + 1})">Siguiente</a>
               </li>`;
               
      list.innerHTML = html;
    }

    // Global function for pagination click
    window.changePage = function(type, page) {
      if (type === 'entrada') {
        currentPageEntrada = page;
        renderDocumentosTable();
      } else {
        currentPageSalida = page;
        renderSalidaDocumentosTable();
      }
    };

    function renderDocumentosTable() {
      const tbody = document.getElementById('documentosTableBody');
      const filteredDocs = getFilteredDocuments('entrada');

      // Pagination Logic
      const totalPages = Math.ceil(filteredDocs.length / itemsPerPage);
      if (currentPageEntrada > totalPages) currentPageEntrada = 1;
      if (currentPageEntrada < 1) currentPageEntrada = 1;
      
      const start = (currentPageEntrada - 1) * itemsPerPage;
      const paginatedDocs = filteredDocs.slice(start, start + itemsPerPage);
      
      renderPaginationControls('entrada', totalPages);

      if (filteredDocs.length === 0) {
        tbody.innerHTML = '<tr class="text-center"><td colspan="15" class="py-3">No hay documentos registrados</td></tr>';
        return;
      }
      
      tbody.innerHTML = paginatedDocs.map((doc, index) => {
        const statusClass = doc.condicion === 'archivado' ? 'bg-archivado' : 'bg-proceso';
        const statusText = doc.condicion === 'archivado' ? 'Archivado' : 'En proceso';
        const canEditObs = currentUser && (currentUser.role === 'admin' || currentUser.role === 'jefe_division');
        
        const asignacionInfo = doc.assignedUsers 
          ? doc.assignedUsers.split(', ').map(u => getUserDisplayName(u)).join(', ')
          : getDivisionDisplay(doc.division);

        return `
        <tr>
          <td>${start + index + 1}</td>
          <td>${SecurityFirewall.sanitize(doc.numeroOficio || '-')}</td>
          <td>${getAsuntoDisplay(doc.asunto)}</td>
          <td>${getTipoDocumentoDisplay(doc.tipoDocumento)}</td>
          <td>${doc.fecha}</td>
          <td>${SecurityFirewall.sanitize(doc.destinatario)}</td>
          <td>${SecurityFirewall.sanitize(doc.profesionalRegistro || '-')}</td>
          <td><span class="badge bg-secondary">${SecurityFirewall.sanitize(asignacionInfo)}</span></td>
          <td>${SecurityFirewall.sanitize(doc.accion || '-')}</td>
          <td>${SecurityFirewall.sanitize(doc.descripcion)}</td>
          <td class="text-center">
            ${doc.archivos && doc.archivos.length > 0 ? doc.archivos.map((f, i) => 
              `<i class="fas fa-paperclip text-primary pointer-cursor me-1" onclick="viewDocument(${doc.id}, 'entrada', ${i})" title="${f.nombre}"></i>`
            ).join('') : '<span class="text-muted">-</span>'}
          </td>
          <td>
            ${currentUser && currentUser.role === 'admin' ? `
              <button class="btn btn-sm btn-warning" onclick="editDocumento(${doc.id})">Editar</button>
              <button class="btn btn-sm btn-danger" onclick="deleteDocumento(${doc.id})">Eliminar</button>
            ` : currentUser && currentUser.role === 'jefe_division' ? `
              <button class="btn btn-sm btn-success" onclick="editDocumento(${doc.id})"><i class="fas fa-tasks me-1"></i>Asignar</button>
              <button class="btn btn-sm btn-info text-white" onclick="viewDocumentDetails(${doc.id}, 'entrada')"><i class="fas fa-eye me-1"></i>Ver</button>
            ` : `
              <button class="btn btn-sm btn-info text-white" onclick="viewDocumentDetails(${doc.id}, 'entrada')"><i class="fas fa-eye me-1"></i>Ver</button>
            `}
          </td>
          <td><input type="text" class="form-control form-control-sm form-sm" value="${SecurityFirewall.sanitize(doc.observacion || '')}" onchange="updateObservacion(${doc.id}, this.value, 'entrada')" placeholder="Observación" ${!canEditObs ? 'disabled' : ''}></td>
          <td>
            <div class="d-flex align-items-center">
              ${(currentUser.role === 'admin' || currentUser.role === 'jefe_division') ? 
                getStatusDotHtml(doc) : 
                (currentUser.role === 'usuario_dep' ? 
                  `<select class="form-select form-select-sm" onchange="updateCondicion(${doc.id}, this.value, 'entrada')">
                    <option value="en_proceso" ${doc.condicion === 'en_proceso' || !doc.condicion ? 'selected' : ''}>En proceso</option>
                    <option value="archivado" ${doc.condicion === 'archivado' ? 'selected' : ''}>Finalizado</option>
                  </select>` : 
                  `<span class="badge-status ${statusClass}">${statusText}</span>`
                )
              }
            </div>
          </td>
        </tr>
      `}).join('');
    }

    function renderSalidaDocumentosTable() {
      const tbody = document.getElementById('salidaDocumentosTableBody');
      const filteredDocs = getFilteredDocuments('salida');

      // Pagination Logic
      const totalPages = Math.ceil(filteredDocs.length / itemsPerPage);
      if (currentPageSalida > totalPages) currentPageSalida = 1;
      if (currentPageSalida < 1) currentPageSalida = 1;
      
      const start = (currentPageSalida - 1) * itemsPerPage;
      const paginatedDocs = filteredDocs.slice(start, start + itemsPerPage);
      
      renderPaginationControls('salida', totalPages);

      if (filteredDocs.length === 0) {
        tbody.innerHTML = '<tr class="text-center"><td colspan="17" class="py-3">No hay documentos de salida registrados</td></tr>'; 
        return;
      }
tbody.innerHTML = paginatedDocs.map((doc, index) => {
        const statusClass = doc.condicion === 'archivado' ? 'bg-archivado' : 'bg-proceso';
        const statusText = doc.condicion === 'archivado' ? 'Archivado' : 'En proceso';
        const canEditObs = currentUser && (currentUser.role === 'admin' || currentUser.role === 'jefe_division');
        
        const asignacionInfo = doc.assignedUsers 
          ? doc.assignedUsers.split(', ').map(u => getUserDisplayName(u)).join(', ')
          : getDivisionDisplay(doc.division);

        return `
        <tr>
          <td>${start + index + 1}</td>
          <td>${SecurityFirewall.sanitize(doc.numeroOficio || '-')}</td>
          <td>${getAsuntoDisplay(doc.asunto)}</td>
          <td>${getTipoDocumentoDisplay(doc.tipoDocumento)}</td>
          <td>${doc.fecha}</td>
          <td>${SecurityFirewall.sanitize(doc.destinatario)}</td>
          <td><span class="badge bg-secondary">${SecurityFirewall.sanitize(asignacionInfo)}</span></td>
          <td>${SecurityFirewall.sanitize(doc.accion || '-')}</td>
          <td>${SecurityFirewall.sanitize(doc.profesional || '-')}</td>
          <td>${doc.fechaEntrega || '-'}</td>
          <td>${SecurityFirewall.sanitize(doc.descripcion)}</td>
          <td class="text-center">
            ${doc.archivos && doc.archivos.length > 0 ? doc.archivos.map((f, i) => 
              `<i class="fas fa-paperclip text-primary pointer-cursor me-1" onclick="viewDocument(${doc.id}, 'salida', ${i})" title="${f.nombre}"></i>`
            ).join('') : '<span class="text-muted">-</span>'}
          </td>
          <td>
            ${currentUser && currentUser.role === 'admin' ? `
              <button class="btn btn-sm btn-warning" onclick="editDocumentoSalida(${doc.id})">Editar</button>
              <button class="btn btn-sm btn-danger" onclick="deleteDocumentoSalida(${doc.id})">Eliminar</button>
            ` : currentUser && currentUser.role === 'jefe_division' ? `
              <button class="btn btn-sm btn-success" onclick="editDocumentoSalida(${doc.id})"><i class="fas fa-tasks me-1"></i>Asignar</button>
              <button class="btn btn-sm btn-info text-white" onclick="viewDocumentDetails(${doc.id}, 'salida')"><i class="fas fa-eye me-1"></i>Ver</button>
            ` : `
              <button class="btn btn-sm btn-info text-white" onclick="viewDocumentDetails(${doc.id}, 'salida')"><i class="fas fa-eye me-1"></i>Ver</button>
            `}
          </td>
          <td><input type="text" class="form-control form-control-sm form-sm" value="${SecurityFirewall.sanitize(doc.observacion || '')}" onchange="updateObservacion(${doc.id}, this.value, 'salida')" placeholder="Observación" ${!canEditObs ? 'disabled' : ''}></td>
          <td>
            <div class="d-flex align-items-center">
              ${(currentUser.role === 'admin' || currentUser.role === 'jefe_division') ? 
                getStatusDotHtml(doc) : 
                (currentUser.role === 'usuario_dep' ? 
                  `<select class="form-select form-select-sm" onchange="updateCondicion(${doc.id}, this.value, 'salida')">
                    <option value="en_proceso" ${doc.condicion === 'en_proceso' || !doc.condicion ? 'selected' : ''}>En proceso</option>
                    <option value="archivado" ${doc.condicion === 'archivado' ? 'selected' : ''}>Finalizado</option>
                  </select>` : 
                  `<span class="badge-status ${statusClass}">${statusText}</span>`
                )
              }
            </div>
          </td>
        </tr>
      `}).join('');
    }

    function viewDocumentDetails(id, type) {
      const docs = type === 'entrada' ? documentos : documentosSalida;
      const doc = docs.find(d => d.id === id);
      
      if (doc) {
        populateUserSelects();
        renderUserCheckboxes('editAsignacionUsuarios');
        
        document.getElementById('editId').value = doc.id;
        document.getElementById('editType').value = type;
        document.getElementById('editDestinatario').value = doc.destinatario;
        document.getElementById('editFecha').value = doc.fecha;
        document.getElementById('editNumeroOficio').value = doc.numeroOficio || '';
        document.getElementById('editTipoDocumento').value = doc.tipoDocumento;
        document.getElementById('editAsunto').value = doc.asunto;
        document.getElementById('editProfesionalRegistro').value = doc.profesionalRegistro || '';
        document.getElementById('editDescripcion').value = doc.descripcion;
        setMultipleSelectValues('editAccion', doc.accion || '');
        setMultipleSelectValues('editAsignacionUsuarios', doc.assignedUsers || '');
        
        if (type === 'entrada') {
          document.getElementById('editFechaEntregaContainer').classList.add('d-none');
          document.getElementById('editProfesionalContainer').classList.add('d-none');
          document.getElementById('editProfesionalRegistroContainer').classList.remove('d-none');
        } else {
          document.getElementById('editFechaEntrega').value = doc.fechaEntrega || '';
          document.getElementById('editProfesional').value = doc.profesional || '';
          document.getElementById('editFechaEntregaContainer').classList.remove('d-none');
          document.getElementById('editProfesionalContainer').classList.remove('d-none');
          document.getElementById('editProfesionalRegistroContainer').classList.add('d-none');
        }
        

        // Set Read Only UI
        const form = document.getElementById('editDocumentForm');
        const elements = form.elements;
        Array.from(elements).forEach(element => {
            element.disabled = true;
        });
        document.getElementById('btnSaveEdit').classList.add('d-none');
        document.querySelector('#editarDocumentoModal .modal-title').textContent = 'Detalles del Documento';
        
        initUserAssignmentContainers();
        const editModal = bootstrap.Modal.getOrCreateInstance(document.getElementById('editarDocumentoModal'));
        editModal.show();
        
        document.getElementById('editarDocumentoModal').addEventListener('shown.bs.modal', function() {
          initUserAssignmentContainers();
        }, { once: true });
      }
    }

    function editDocumento(id) {
      const doc = documentos.find(d => d.id === id);
      if (doc) {
        // Reset UI to Edit Mode
        renderUserCheckboxes('editAsignacionUsuarios');
        
        const isJefe = currentUser.role === 'jefe_division';

        const form = document.getElementById('editDocumentForm');
        const elements = form.elements;
        Array.from(elements).forEach(element => {
            element.disabled = false;
        });
        // Campos bloqueados para el Jefe (Estructura de Solo Lectura para lo que no le compete)
        document.getElementById('editDestinatario').disabled = isJefe;
        document.getElementById('editFecha').disabled = isJefe;
        document.getElementById('editNumeroOficio').disabled = isJefe;
        document.getElementById('editTipoDocumento').disabled = isJefe;
        document.getElementById('editAsunto').disabled = isJefe;
        document.getElementById('editProfesionalRegistro').disabled = isJefe;
        document.getElementById('editDescripcion').disabled = isJefe;
        
        document.querySelectorAll('#editAccion input').forEach(i => i.disabled = false);

        document.getElementById('btnSaveEdit').classList.remove('d-none');
        document.querySelector('#editarDocumentoModal .modal-title').textContent = isJefe ? 'Derivar Documento (Instrucción)' : 'Editar Documento';

        populateUserSelects();
        document.getElementById('editId').value = doc.id;
        document.getElementById('editType').value = 'entrada';
        document.getElementById('editDestinatario').value = doc.destinatario;
        document.getElementById('editFecha').value = doc.fecha;
        document.getElementById('editNumeroOficio').value = doc.numeroOficio || '';
        document.getElementById('editTipoDocumento').value = doc.tipoDocumento;
        document.getElementById('editAsunto').value = doc.asunto;
        document.getElementById('editProfesionalRegistro').value = doc.profesionalRegistro || '';
        setMultipleSelectValues('editAccion', doc.accion || '');
        setMultipleSelectValues('editAsignacionUsuarios', doc.assignedUsers || '');
        document.getElementById('editDescripcion').value = doc.descripcion;
        document.getElementById('editFechaEntregaContainer').classList.add('d-none');
        document.getElementById('editProfesionalRegistroContainer').classList.remove('d-none');

        document.getElementById('editProfesionalContainer').classList.add('d-none');
        const editModal = bootstrap.Modal.getOrCreateInstance(document.getElementById('editarDocumentoModal'));
        editModal.show();
      }
    }

    function deleteDocumento(id) {
      Swal.fire({
        title: '¿Eliminar documento?',
        text: "Esta acción no se puede deshacer.",
        icon: 'warning',
        showCancelButton: true,
        confirmButtonColor: '#c0392b',
        confirmButtonText: 'Sí, eliminar',
        cancelButtonText: 'Cancelar'
      }).then((result) => {
        if (result.isConfirmed) {
          documentos = documentos.filter(d => d.id !== id);
          saveToLocalStorage('documentos', documentos);
          renderDocumentosTable();
          updateHomeStats();
          Swal.fire('Eliminado', 'El documento ha sido borrado.', 'success');
        }
      });
    }

    function editDocumentoSalida(id) {
      const doc = documentosSalida.find(d => d.id === id);
      if (doc) {
        // Reset UI to Edit Mode
        renderUserCheckboxes('editAsignacionUsuarios');

        const isJefe = currentUser.role === 'jefe_division';
        
        const form = document.getElementById('editDocumentForm');
        const elements = form.elements;
        Array.from(elements).forEach(element => {
            element.disabled = false;
        });
        document.getElementById('editDestinatario').disabled = isJefe;
        document.getElementById('editFecha').disabled = isJefe;
        document.getElementById('editNumeroOficio').disabled = isJefe;
        document.querySelectorAll('#editAccion input').forEach(i => i.disabled = isJefe);
        document.getElementById('editAsunto').disabled = isJefe;
        document.getElementById('editDescripcion').disabled = isJefe;
        document.getElementById('editProfesional').disabled = isJefe;
        document.getElementById('editFechaEntrega').disabled = isJefe;
        
        document.getElementById('btnSaveEdit').classList.remove('d-none');
        document.querySelector('#editarDocumentoModal .modal-title').textContent = isJefe ? 'Derivar Documento (Instrucción)' : 'Editar Documento';

        populateUserSelects();
        document.getElementById('editId').value = doc.id;
        document.getElementById('editType').value = 'salida';
        document.getElementById('editDestinatario').value = doc.destinatario;
        document.getElementById('editFecha').value = doc.fecha;
        document.getElementById('editNumeroOficio').value = doc.numeroOficio || '';
        document.getElementById('editTipoDocumento').value = doc.tipoDocumento;
        document.getElementById('editAsunto').value = doc.asunto;
        setMultipleSelectValues('editAccion', doc.accion || '');
        setMultipleSelectValues('editAsignacionUsuarios', doc.assignedUsers || '');
        document.getElementById('editFechaEntrega').value = doc.fechaEntrega || '';
        document.getElementById('editDescripcion').value = doc.descripcion;
        document.getElementById('editProfesional').value = doc.profesional || '';

        document.getElementById('editFechaEntregaContainer').classList.remove('d-none');
        document.getElementById('editProfesionalContainer').classList.remove('d-none');
        const editModal = bootstrap.Modal.getOrCreateInstance(document.getElementById('editarDocumentoModal'));
        editModal.show();
      }
    }

    function deleteDocumentoSalida(id) {
      Swal.fire({
        title: '¿Eliminar documento de salida?',
        text: "Esta acción borrará el registro permanentemente.",
        icon: 'warning',
        showCancelButton: true,
        confirmButtonColor: '#c0392b',
        confirmButtonText: 'Sí, eliminar',
        cancelButtonText: 'Cancelar'
      }).then((result) => {
        if (result.isConfirmed) {
          documentosSalida = documentosSalida.filter(d => d.id !== id);
          saveToLocalStorage('documentosSalida', documentosSalida);
          renderSalidaDocumentosTable();
          updateHomeStats();
          Swal.fire('Eliminado', 'El documento de salida ha sido borrado.', 'success');
        }
      });
    }

    // Export to Excel functionality
    document.getElementById('exportarExcel').addEventListener('click', function() {
      // Filter documents by current user's department (admins see all)
      const filteredDocs = currentUser && currentUser.role !== 'admin' && currentUser.departamento
        ? documentos.filter(doc => doc.departamento === currentUser.departamento)
        : documentos;
      exportToExcel(filteredDocs, 'documentos_entrada.xlsx');
    });

    document.getElementById('exportarExcelSalida').addEventListener('click', function() {
      // Filter documents by current user's department (admins see all)
      const filteredDocs = currentUser && currentUser.role !== 'admin' && currentUser.departamento
        ? documentosSalida.filter(doc => doc.departamento === currentUser.departamento)
        : documentosSalida;
      exportToExcel(filteredDocs, 'documentos_salida.xlsx', true);
    });

    // Search functionality listeners
    // Auto-correlativo para Documentos de Salida
    document.getElementById('salidaTipoDocumento').addEventListener('change', function() {
      const tipo = this.value;
      const numInput = document.getElementById('salidaNumeroOficio');
      
      if (!tipo) {
        numInput.value = '';
        return;
      }

      const yearActual = new Date().getFullYear();
      
      // Filtrar documentos del mismo tipo registrados en el año actual
      const relacionados = documentosSalida.filter(d => {
        const fechaDoc = parseDate(d.fecha);
        return d.tipoDocumento === tipo && fechaDoc && fechaDoc.getFullYear() === yearActual;
      });

      let maxCorrelativo = 0;
      relacionados.forEach(d => {
        const match = (d.numeroOficio || '').match(/^(\d+)/);
        if (match) {
          const valor = parseInt(match[1]);
          if (valor > maxCorrelativo) maxCorrelativo = valor;
        }
      });

      const siguiente = (maxCorrelativo + 1).toString().padStart(3, '0');
      numInput.value = `${siguiente}-${yearActual}`;
    });

    document.getElementById('btnSearch').addEventListener('click', function() {
      currentPageEntrada = 1;
      renderDocumentosTable();
    });

    document.getElementById('btnSearchSalida').addEventListener('click', function() {
      currentPageSalida = 1;
      renderSalidaDocumentosTable();
    });

    // Home Section Buttons
    document.getElementById('btnViewDocs').addEventListener('click', function() {
      showSection('documentsSection');
    });

    document.getElementById('searchInput').addEventListener('input', debounce(function() {
      currentPageEntrada = 1; // Reset to first page on search
      renderDocumentosTable();
    }, 300));
    document.getElementById('searchInputSalida').addEventListener('input', debounce(function() {
      currentPageSalida = 1; // Reset to first page on search
      renderSalidaDocumentosTable();
    }, 300));

    // Helper functions for display text
    // Helper functions for display (centralizadas arriba to avoid duplicates)
    // Note: These functions were defined previously in the file; ensure only one definition exists.
    function exportToExcel(data, filename, isSalida = false) {
      if (data.length === 0) {
        Swal.fire('Advertencia', 'No hay datos para exportar', 'warning');
        return;
      }

      // Create export data matching table display
      const exportData = data.map((doc, index) => {
        const condicionDisplay = doc.condicion === 'en_proceso' ? 'En proceso' : doc.condicion === 'archivado' ? 'Archivado' : '-';
        
        // Calcular estatus de tiempo para el reporte
        let tiempoEstatus = 'En proceso (Al día)';
        if (doc.condicion === 'archivado') {
          tiempoEstatus = 'Finalizado';
        } else {
          const regTime = doc.id || Date.now();
          if (Date.now() - regTime > 24 * 60 * 60 * 1000) {
            tiempoEstatus = 'Atrasado (+24h)';
          }
        }

        if (isSalida) {
          return {
            'N°': index + 1,
            'Número de Oficio': doc.numeroOficio || '-',
            'Asunto': getAsuntoDisplay(doc.asunto),
            'Tipo de Documento': getTipoDocumentoDisplay(doc.tipoDocumento),
            'Fecha de Emisión': doc.fecha,
            'Unidad de Destino': doc.destinatario,
            'División Responsable': getDivisionDisplay(doc.division),
            'Departamento': getDepartamentoDisplay(doc.departamento),
            'Profesional que lo elaboró': doc.profesional || '-',
            'Resumen/Descripción': doc.descripcion,
            'Archivo Adjunto': doc.archivo ? 'Sí' : 'No',
            'Observación': doc.observacion || '',
            'Condición': condicionDisplay,
            'Estatus Tiempo': tiempoEstatus
          };
        } else {
          return {
            'N°': index + 1,
            'Número de Oficio': doc.numeroOficio || '-',
            'Asunto': getAsuntoDisplay(doc.asunto),
            'Tipo de Documento': getTipoDocumentoDisplay(doc.tipoDocumento),
            'Fecha de Recepción': doc.fecha,
            'Unidad de Procedencia': doc.destinatario,
            'División Responsable': getDivisionDisplay(doc.division),
            'Departamento': getDepartamentoDisplay(doc.departamento),
            'Acción': doc.accion || '-',
            'Resumen/Descripción': doc.descripcion,
            'Archivo Adjunto': doc.archivo ? 'Sí' : 'No',
            'Observación': doc.observacion || '',
            'Condición': condicionDisplay,
            'Estatus Tiempo': tiempoEstatus
          };
        }
      });

      // Create a new workbook
      const wb = XLSX.utils.book_new();

      // Convert data to worksheet
      const ws = XLSX.utils.json_to_sheet(exportData);

      // Add worksheet to workbook
      XLSX.utils.book_append_sheet(wb, ws, 'Documentos');

      // Write file
      XLSX.writeFile(wb, filename);

      Swal.fire('Éxito', 'Archivo Excel exportado correctamente', 'success');
    }

    // Función para exportar relación periódica (Semanal, Quincenal, Mensual)
    window.exportPeriodico = function(type, period) {
      const isSalida = type === 'salida';
      const allDocs = isSalida ? documentosSalida : documentos;
      
      // Aplicar filtros de permisos del usuario actual
      let docs = applyUserFilters(allDocs);
      
      const now = new Date();
      let days = 7;
      if (period === 'quincenal') days = 15;
      else if (period === 'mensual') days = 30;
      
      const threshold = new Date();
      threshold.setDate(now.getDate() - days);
      threshold.setHours(0, 0, 0, 0);
      
      const filtered = docs.filter(doc => {
        const docDate = parseDate(doc.fecha);
        return docDate && docDate >= threshold && docDate <= now;
      });
      
      if (filtered.length === 0) {
        Swal.fire('Información', `No se encontraron documentos en el periodo ${period} seleccionado.`, 'info');
        return;
      }
      
      const typeLabel = isSalida ? 'Salida' : 'Entrada';
      const periodLabel = period.charAt(0).toUpperCase() + period.slice(1);
      const filename = `Relacion_${typeLabel}_${periodLabel}.xlsx`;
      
      exportToExcel(filtered, filename, isSalida);
    }

    // File upload functionality
    function initializeFileUpload(dropzoneId, fileInputId, previewContainerId, previewContentId) {
      const dropzone = document.getElementById(dropzoneId);
      const fileInput = document.getElementById(fileInputId);
      const previewContainer = document.getElementById(previewContainerId);
      const previewContent = document.getElementById(previewContentId);

      // Click on dropzone to open file dialog
      dropzone.addEventListener('click', function() {
        fileInput.click();
      });

      // Drag and drop events
      dropzone.addEventListener('dragover', function(e) {
        e.preventDefault();
        dropzone.classList.add('dragover');
      });

      dropzone.addEventListener('dragleave', function(e) {
        e.preventDefault();
        dropzone.classList.remove('dragover');
      });

      dropzone.addEventListener('drop', function(e) {
        e.preventDefault();
        dropzone.classList.remove('dragover');
        const files = e.dataTransfer.files;
        if (files.length > 0) {
          handleFileSelection(files, previewContent, previewContainer);
          // Update file input
          fileInput.files = files;
        }
      });

      // File input change
      fileInput.addEventListener('change', function(e) {
        const smartFillBtn = document.getElementById(fileInputId === 'documentFile' ? 'btnSmartFill' : 'btnSmartFillSalida');
        if (e.target.files.length > 0) {
          handleFileSelection(e.target.files, previewContent, previewContainer);
          if (smartFillBtn) smartFillBtn.classList.remove('d-none');
        } else {
          if (smartFillBtn) smartFillBtn.classList.add('d-none');
        }
      });
    }
    function handleFileSelection(files, previewContent, previewContainer) {
      if (!files || files.length === 0) return;
      
      const fileList = Array.from(files);
      previewContent.innerHTML = fileList.map(file => {
        const isImg = file.type.startsWith('image/');
        return `
          <div class="mb-2 p-2 border rounded d-flex align-items-center bg-white">
            <i class="fas ${isImg ? 'fa-image' : 'fa-file-pdf'} me-2 text-primary"></i>
            <span class="small text-truncate" style="max-width: 200px;">${file.name}</span>
            <span class="badge bg-secondary ms-auto">${(file.size / 1024).toFixed(1)} KB</span>
          </div>
        `;
      }).join('');
      
      previewContainer.classList.remove('d-none');
    }

    // --- MEJORA: IA PERSISTENTE PARA ESCANEO RÁPIDO ---
    let ocrWorker = null;
    async function getOCRWorker() {
      if (!ocrWorker) {
        ocrWorker = await Tesseract.createWorker('spa', 1, {
          logger: m => {
            const status = document.getElementById('ocrStatus');
            if (status && m.status === 'recognizing text') status.textContent = `Leyendo: ${Math.round(m.progress * 100)}%`;
          }
        });
      }
      return ocrWorker;
    }

    // --- Motor de Extracción de Información (OCR) ---
    async function processDocumentExtraction(inputId, formType) {
      const fileInput = document.getElementById(inputId);
      const file = fileInput.files[0];
      if (!file) {
        Swal.fire('Atención', 'Por favor, seleccione o escanee un archivo primero.', 'warning');
        return;
      }

      const isSalida = formType === 'salida';
      const loader = document.getElementById(isSalida ? 'ocrLoaderSalida' : 'ocrLoader');
      const progressBar = document.getElementById(isSalida ? 'ocrProgressBarSalida' : 'ocrProgressBar');
      const statusText = document.getElementById('ocrStatus');

      if (loader) loader.classList.remove('d-none');
      
      try {
        const worker = await getOCRWorker();
        
        // Listener de progreso
        const logger = m => {
          if (m.status === 'recognizing text' && progressBar) {
            const progress = Math.round(m.progress * 100);
            progressBar.style.width = progress + '%';
            if(statusText) statusText.textContent = `Analizando: ${progress}%`;
          }
        };

        const { data: { text } } = await worker.recognize(file);

        // Procesar el texto extraído
        autoFillFormWithText(text, formType);
        
        Swal.fire({
          title: '¡Análisis Completado!',
          text: 'Se han rellenado los campos detectados automáticamente.',
          icon: 'success',
          toast: true,
          position: 'top-end',
          timer: 3000,
          showConfirmButton: false
        });

      } catch (error) {
        console.error("Error OCR:", error);
        Swal.fire('Error', 'No se pudo leer el contenido del documento. Intente con una imagen más clara.', 'error');
      } finally {
        if (loader) loader.classList.add('d-none');
        if (progressBar) progressBar.style.width = '0%';
      }
    }

    function autoFillFormWithText(text, type) {
      const isSalida = type === 'salida';
      
      // 1. Extraer Número de Oficio (Ej: Oficio N° 123-2024)
      const oficioMatch = text.match(/(?:Oficio|N[o°]|Nro|Documento)\s*[:.-]?\s*(\d+[-\w\/]+)/i);
      if (oficioMatch) {
        const elId = isSalida ? 'salidaNumeroOficio' : 'numeroOficio';
        const el = document.getElementById(elId);
        if (el) el.value = oficioMatch[1];
      }

      // 2. Extraer Fecha (Formatos DD/MM/YYYY o DD-MM-YYYY)
      const fechaMatch = text.match(/(\d{1,2}[\/\-\.](?:\d{1,2}|[a-zA-Z]{3,10})[\/\-\.]\d{2,4})/);
      if (fechaMatch) {
        const elId = isSalida ? 'salidaFecha' : 'fecha';
        const el = document.getElementById(elId);
        if (el) el.value = fechaMatch[1];
      }

      // 3. Detectar Tipo de Documento
      const tipos = {
        'oficio': /oficio/i,
        'memorandum': /(memor[áa]ndum|memo)/i,
        'circular': /circular/i,
        'informe': /informe/i,
        'agenda': /agenda/i,
        'radiograma': /radiograma/i,
        'punto_cuenta': /punto\s+de\s+cuenta/i,
        'acta': /acta/i
      };
      for (const [key, regex] of Object.entries(tipos)) {
        if (regex.test(text)) {
          const el = document.getElementById(isSalida ? 'salidaTipoDocumento' : 'tipoDocumento');
          if (el) el.value = key;
          break;
        }
      }

      // 4. Detectar Asunto probable
      const asuntos = {
        'presentacion': /presenta/i,
        'informacion': /informa/i,
        'remision': /remiti/i,
        'solicitud': /solicit/i
      };
      for (const [key, regex] of Object.entries(asuntos)) {
        if (regex.test(text)) {
          const el = document.getElementById(isSalida ? 'salidaAsunto' : 'asunto');
          if (el) el.value = key;
          break;
        }
      }
    }

    // Initialize file uploads (guard against missing elements)
    if (document.getElementById('dropzone') && document.getElementById('documentFile')) {
      initializeFileUpload('dropzone', 'documentFile', 'previewContainer', 'previewContent');
    }
    if (document.getElementById('salidaDropzone') && document.getElementById('salidaDocumentFile')) {
      initializeFileUpload('salidaDropzone', 'salidaDocumentFile', 'salidaPreviewContainer', 'salidaPreviewContent');
    }

    // Reset preview on form reset
    document.getElementById('documentForm').addEventListener('reset', function() {
      document.getElementById('previewContainer').classList.add('d-none');
      document.getElementById('previewContent').innerHTML = ''; // Clear content
      document.getElementById('asignacionUsuarios').querySelectorAll('input[type="checkbox"]').forEach(cb => cb.checked = false);
      document.getElementById('accion').querySelectorAll('input[type="checkbox"]').forEach(cb => cb.checked = false);
      flatpickr('#fecha').clear();
    });

    document.getElementById('salidaForm').addEventListener('reset', function() {
      document.getElementById('salidaPreviewContainer').classList.add('d-none');
      document.getElementById('salidaPreviewContent').innerHTML = ''; // Clear content
      document.getElementById('salidaAsignacionUsuarios').querySelectorAll('input[type="checkbox"]').forEach(cb => cb.checked = false);
      document.getElementById('salidaAccion').querySelectorAll('input[type="checkbox"]').forEach(cb => cb.checked = false);
      flatpickr('#salidaFecha').clear();
      flatpickr('#salidaFechaEntrega').clear();
    });

    // View document functionality
    function viewDocument(id, type, fileIndex = 0) {
      const docs = type === 'entrada' ? documentos : documentosSalida;
      const doc = docs.find(d => d.id === id);

      const targetFile = doc && doc.archivos ? doc.archivos[fileIndex] : null;

      if (targetFile && targetFile.datos) {
        try {
          // Create a blob from the base64 data safely
          const parts = targetFile.datos.split(',');
          if (parts.length < 2) throw new Error('Datos de archivo inválidos');
          
          const byteCharacters = atob(parts[1]);
          const byteNumbers = new Array(byteCharacters.length);
          for (let i = 0; i < byteCharacters.length; i++) {
            byteNumbers[i] = byteCharacters.charCodeAt(i);
          }
          const byteArray = new Uint8Array(byteNumbers);
          const blob = new Blob([byteArray], { type: targetFile.tipo });

          // Create a URL for the blob
          const url = URL.createObjectURL(blob);
          window.open(url, '_blank');

          // Clean up the URL after a delay
          setTimeout(() => URL.revokeObjectURL(url), 1000);
        } catch (e) {
          console.error(e);
          Swal.fire('Error', 'El archivo está dañado o tiene un formato incorrecto.', 'error');
        }
      } else {
        Swal.fire('Error', 'No se encontró el documento adjunto', 'error');
      }
    }

    // --- Funciones de Escáner ---
    let currentStream = null;
    let targetInputId = null;
    let targetPreviewContainerId = null;
    let targetPreviewContentId = null;

    function openScanner(inputId, previewContainerId, previewContentId) {
      targetInputId = inputId;
      targetPreviewContainerId = previewContainerId;
      targetPreviewContentId = previewContentId;
      const modal = bootstrap.Modal.getOrCreateInstance(document.getElementById('scannerModal'));
      modal.show();
      
      navigator.mediaDevices.getUserMedia({ video: { facingMode: "environment" } })
        .then(stream => {
          currentStream = stream;
          document.getElementById('scannerVideo').srcObject = stream;
          const video = document.getElementById('scannerVideo');
          video.playsInline = true; // Propiedad estándar de JS
          video.setAttribute('webkit-playsinline', 'true'); // Soporte para WebKit antiguo
          video.srcObject = stream;
        })
        .catch(err => {
          Swal.fire('Error', 'No se pudo acceder a la cámara: ' + err.message, 'error');
          modal.hide();
        });
    }

    function stopCamera() {
      if (currentStream) {
        currentStream.getTracks().forEach(track => track.stop());
        currentStream = null;
      }
    }

    function captureImage() {
      const video = document.getElementById('scannerVideo');
      const canvas = document.getElementById('scannerCanvas');
      canvas.width = video.videoWidth;
      canvas.height = video.videoHeight;
      canvas.getContext('2d').drawImage(video, 0, 0);
      
      const dataUrl = canvas.toDataURL('image/jpeg');
      const blob = dataURItoBlob(dataUrl);
      const file = new File([blob], "escaneo_" + Date.now() + ".jpg", { type: "image/jpeg" });

      const container = new DataTransfer();
      container.items.add(file);
      document.getElementById(targetInputId).files = container.files;
      
      handleFileSelection([file], document.getElementById(targetPreviewContentId), document.getElementById(targetPreviewContainerId));
      bootstrap.Modal.getInstance(document.getElementById('scannerModal')).hide();
      stopCamera();
    }

    // --- Funciones para Escáner Físico (Impresoras/TWAIN) ---
    function scanFromPhysicalScanner(inputId, previewContainerId, previewContentId) {
      if (typeof scanner === 'undefined') {
        Swal.fire({
          title: 'Cargando controlador...',
          text: 'Preparando la conexión con el escáner físico.',
          didOpen: () => { Swal.showLoading(); }
        });

        const script = document.createElement('script');
        script.src = 'https://cdn.asprise.com/scannerjs/scanner.js';
        script.onload = () => {
          Swal.close();
          executePhysicalScan(inputId, previewContainerId, previewContentId);
        };
        script.onerror = () => { Swal.fire('Error', 'No se pudo cargar el módulo de escaneo físico.', 'error'); };
        document.head.appendChild(script);
      } else {
        executePhysicalScan(inputId, previewContainerId, previewContentId);
      }
    }

    function executePhysicalScan(inputId, previewContainerId, previewContentId) {
      Swal.fire({
        title: 'Iniciando escáner...',
        text: 'Por favor, asegúrese de que su escáner esté encendido y conectado.',
        allowOutsideClick: false,
        didOpen: () => {
          Swal.showLoading();
        }
      });

      scanner.scan(
        (successful, mesg, response) => {
          Swal.close();
          if (!successful) {
            if (mesg.includes('Scanner.js service is not running')) {
              Swal.fire('Servicio no encontrado', 'Para usar el escáner físico debe tener instalado el agente local. ¿Desea descargarlo?', 'info')
                .then(() => window.open('https://asprise.com/document-scan-upload-image-browser/direct-wia-twain-scanner-access-js-html5.html', '_blank'));
            } else {
              Swal.fire('Error de Escáner', mesg, 'error');
            }
            return;
          }

          const images = scanner.getImagesFromResponse(response);
          if (images && images.length > 0) {
            // 1. Convertir la imagen escaneada a un archivo File real
            const imageData = images[0].src;
            const file = new File([dataURItoBlob(imageData)], `scan_${Date.now()}.jpg`, { type: "image/jpeg" });

            // 2. Adjuntar el archivo al input del sistema mediante DataTransfer
            const input = document.getElementById(inputId);
            const dt = new DataTransfer();
            dt.items.add(file);
            input.files = dt.files;
            
            // 3. Notificar al sistema del cambio y mostrar vista previa
            input.dispatchEvent(new Event('change', { bubbles: true }));
            handleFileSelection([file], document.getElementById(previewContentId), document.getElementById(previewContainerId));
            
            // 4. Iniciar extracción de datos por IA automáticamente
            const formType = inputId === 'documentFile' ? 'entrada' : 'salida';
            setTimeout(() => processDocumentExtraction(inputId, formType), 500);

            Swal.fire({
              toast: true,
              position: 'top-end',
              icon: 'success',
              title: 'Documento adjuntado y analizado',
              showConfirmButton: false,
              timer: 3000
            });
          }
        },
        {
          "use_asprise_dialog": true, // Permite elegir entre alimentador (ADF) o cama plana
          "output_settings": [
            { "type": "return-base64", "format": "jpg", "jpg_quality": 90 }
          ],
          "config": { "dpi": 300, "pixel_mode": "grayscale" } // Optimizado para documentos y OCR
        }
      );
    }

    function dataURItoBlob(dataURI) {
      const byteString = atob(dataURI.split(',')[1]);
      const ab = new ArrayBuffer(byteString.length);
      const ia = new Uint8Array(ab);
      for (let i = 0; i < byteString.length; i++) ia[i] = byteString.charCodeAt(i);
      return new Blob([ab], {type: 'image/jpeg'});
    }

    // ✅ Step 7: Initialize all user assignment containers + event hooks
    function initUserAssignmentContainers() {
      // Render checkboxes in all 3 containers
      renderUserCheckboxes('asignacionUsuarios');
      renderUserCheckboxes('salidaAsignacionUsuarios');
      renderUserCheckboxes('editAsignacionUsuarios');
      
      // Hook into form navigation events (already called in nav clicks)
      // Hook into edit modal shown event
      const editModalEl = document.getElementById('editarDocumentoModal');
      if (editModalEl) {
        editModalEl.removeEventListener('shown.bs.modal', initUserAssignmentContainers); // Prevent duplicates
        editModalEl.addEventListener('shown.bs.modal', initUserAssignmentContainers);
      }
    }

    // Populate user selects (legacy dropdowns)
    function populateUserSelects() {
      if (!currentUser) return;

      const divs = Object.keys(divisionToDepartments);
      const updateSelect = (id) => {
        const el = document.getElementById(id);
        if (!el) return;
        el.innerHTML = '<option value="">Seleccione una división</option>' + 
          divs.map(d => `<option value="${d}">${getDivisionDisplay(d)}</option>`).join('');
      };

      updateSelect('newDivision');
      updateSelect('editDivision');
    }

    // Dynamic visibility/enablement for user form fields
    function updateUserFormFieldsVisibility(role, divisionContainer, divisionSelect, departamentoContainer, departamentoSelect, clearFields = false) {
      // Reset all to hidden/disabled first
      divisionContainer.classList.add('d-none');
      divisionSelect.disabled = true;
      divisionSelect.required = false;
      departamentoContainer.classList.add('d-none');
      departamentoSelect.disabled = true;
      departamentoSelect.required = false;

      if (role === 'jefe_division') {
        divisionContainer.classList.remove('d-none');
        divisionSelect.disabled = false;
        divisionSelect.required = true;
        departamentoSelect.innerHTML = '<option value="">Seleccione un departamento</option>';
        departamentoSelect.value = '';
      } else if (role === 'usuario_dep') {
        divisionContainer.classList.remove('d-none');
        divisionSelect.disabled = false;
        divisionSelect.required = true;
        departamentoContainer.classList.remove('d-none');
        departamentoSelect.disabled = false;
        departamentoSelect.required = true;
        
        const selectedDivision = divisionSelect.value;
        const departmentsForDivision = divisionToDepartments[selectedDivision] || [];
        
        // Clear existing options
        departamentoSelect.innerHTML = '<option value="">Seleccione un departamento</option>';
        
        departmentsForDivision.forEach(depValue => {
          const option = document.createElement('option');
          option.value = depValue;
          option.textContent = getDepartamentoDisplay(depValue);
          departamentoSelect.appendChild(option);
        });
      }
    }

    // Event listeners for role changes in add user modal
    document.getElementById('newRole').addEventListener('change', function() {
      updateUserFormFieldsVisibility(this.value, document.getElementById('newDivisionContainer'), document.getElementById('newDivision'), document.getElementById('newDepartamentoContainer'), document.getElementById('newDepartamento'), true);
    });

    // Event listener for division changes in add user modal
    document.getElementById('newDivision').addEventListener('change', function() {
      updateUserFormFieldsVisibility(document.getElementById('newRole').value, document.getElementById('newDivisionContainer'), document.getElementById('newDivision'), document.getElementById('newDepartamentoContainer'), document.getElementById('newDepartamento'), true);
    });

    // Event listeners for role changes in edit user modal
    document.getElementById('editRole').addEventListener('change', function() {
      updateUserFormFieldsVisibility(this.value, document.getElementById('editDivisionContainer'), document.getElementById('editDivision'), document.getElementById('editDepartamentoContainer'), document.getElementById('editDepartamento'), true);
    });

    // Event listener for division changes in edit user modal
    document.getElementById('editDivision').addEventListener('change', function() {
      updateUserFormFieldsVisibility(document.getElementById('editRole').value, document.getElementById('editDivisionContainer'), document.getElementById('editDivision'), document.getElementById('editDepartamentoContainer'), document.getElementById('editDepartamento'), true);
    });

    // Also call when modals are shown to ensure correct initial state
    const addUserModalElement = document.getElementById('addUserModal');
    addUserModalElement.addEventListener('show.bs.modal', function () {
      updateUserFormFieldsVisibility(document.getElementById('newRole').value, document.getElementById('newDivisionContainer'), document.getElementById('newDivision'), document.getElementById('newDepartamentoContainer'), document.getElementById('newDepartamento'), false);
    });

    const editUserModalElement = document.getElementById('editUserModal');
    editUserModalElement.addEventListener('show.bs.modal', function () {
      // The editUser function already calls this, but good to have a fallback
      // or ensure it's called after the fields are populated.
      updateUserFormFieldsVisibility(document.getElementById('editRole').value, document.getElementById('editDivisionContainer'), document.getElementById('editDivision'), document.getElementById('editDepartamentoContainer'), document.getElementById('editDepartamento'), false);
    });

    // Handle save edit button
    document.getElementById('btnSaveEdit').addEventListener('click', function() {
      const id = parseInt(document.getElementById('editId').value);
      const type = document.getElementById('editType').value;
      const selectedAccion = getMultipleSelectValues('editAccion');

      const updatedDoc = {
        id: id,
        destinatario: document.getElementById('editDestinatario').value,
        fecha: document.getElementById('editFecha').value,
        numeroOficio: document.getElementById('editNumeroOficio').value,
        tipoDocumento: document.getElementById('editTipoDocumento').value,
        asunto: document.getElementById('editAsunto').value,
        assignedUsers: getMultipleSelectValues('editAsignacionUsuarios'),
        profesionalRegistro: document.getElementById('editProfesionalRegistro').value,
        accion: selectedAccion,
        descripcion: document.getElementById('editDescripcion').value
      };
      

      if (type === 'entrada') {
        // Entrada document (accion already updated in updatedDoc)
        const docIndex = documentos.findIndex(d => d.id === id);
        if (docIndex !== -1) {
          documentos[docIndex] = { ...documentos[docIndex], ...updatedDoc };
          saveToLocalStorage('documentos', documentos);
          populateUserSelects(); // Re-populate if user data changed
          renderDocumentosTable();
        }
      } else {
        // Salida document
        updatedDoc.fechaEntrega = document.getElementById('editFechaEntrega').value;
        updatedDoc.profesional = document.getElementById('editProfesional').value;
        const docIndex = documentosSalida.findIndex(d => d.id === id);
        if (docIndex !== -1) {
          documentosSalida[docIndex] = { ...documentosSalida[docIndex], ...updatedDoc };
          saveToLocalStorage('documentosSalida', documentosSalida);
          populateUserSelects(); // Re-populate if user data changed
          renderSalidaDocumentosTable();
        }
      }

      // Notificar a los usuarios asignados (Derivación)
      const assignedUsers = updatedDoc.assignedUsers;
      if (assignedUsers) {
        assignedUsers.split(', ').forEach(username => {
          if (username.trim()) {
            const notification = {
              id: Date.now() + Math.random(),
              assignedTo: username.trim(),
              message: `Se le ha asignado/derivado un documento (${type === 'entrada' ? 'Entrada' : 'Salida'}): ${updatedDoc.numeroOficio || 'Sin número'} - ${getAsuntoDisplay(updatedDoc.asunto)}`,
              timestamp: new Date().toISOString()
            };
            notifications.push(notification);
            window.dispatchEvent(new CustomEvent('notificationAdded', { detail: notification }));
          }
        });
        saveToLocalStorage('notifications', notifications);
      }

      bootstrap.Modal.getInstance(document.getElementById('editarDocumentoModal')).hide();
      Swal.fire('Éxito', 'Documento actualizado correctamente', 'success');
    });

    // Update observacion function
    function updateObservacion(id, value, type) {
      if (!SecurityFirewall.validateInput(value)) {
        Swal.fire('Alerta', 'Contenido no permitido en observación', 'warning');
        return;
      }
      
      if (!currentUser || (currentUser.role !== 'admin' && currentUser.role !== 'jefe_division')) {
        Swal.fire('Acceso Denegado', 'No tiene permisos para editar observaciones.', 'error');
        return;
      }

      const docs = type === 'entrada' ? documentos : documentosSalida;
      const docIndex = docs.findIndex(d => d.id === parseInt(id));
      if (docIndex !== -1) {
        docs[docIndex].observacion = value;
        saveToLocalStorage(type === 'entrada' ? 'documentos' : 'documentosSalida', docs);
        
        // Refrescar vistas
        if (type === 'entrada') renderDocumentosTable();
        else renderSalidaDocumentosTable();
        updateHomeStats();
        
        // Feedback visual al usuario
        Swal.fire({
          toast: true,
          position: 'top-end',
          icon: 'success',
          title: 'Observación guardada',
          showConfirmButton: false,
          timer: 2000
        });
      }
    }

    // Update condicion function
    function updateCondicion(id, value, type) {
      const docs = type === 'entrada' ? documentos : documentosSalida;
      const docIndex = docs.findIndex(d => d.id === parseInt(id));
      if (docIndex !== -1) {
        const oldCondicion = docs[docIndex].condicion;
        docs[docIndex].condicion = value;
        saveToLocalStorage(type === 'entrada' ? 'documentos' : 'documentosSalida', docs);

        // Notificar a supervisores si se finaliza la tarea
        if (value === 'archivado' && oldCondicion !== 'archivado') {
          const doc = docs[docIndex];
          const notificationMsg = `Documento ${doc.numeroOficio || 'S/N'} ha sido MARCADO COMO FINALIZADO por ${currentUser.username}`;
          
          // Notificar Admin
          const adminNotif = { id: Date.now(), assignedTo: 'admin', message: notificationMsg, timestamp: new Date().toISOString() };
          notifications.push(adminNotif);
          window.dispatchEvent(new CustomEvent('notificationAdded', { detail: adminNotif }));
          
          // Notificar Jefe de División
          const jefe = users.find(u => u.role === 'jefe_division' && u.division === doc.division);
          if (jefe) {
            const jefeNotif = { id: Date.now() + 1, assignedTo: jefe.username, message: notificationMsg, timestamp: new Date().toISOString() };
            notifications.push(jefeNotif);
            window.dispatchEvent(new CustomEvent('notificationAdded', { detail: jefeNotif }));
          }
          saveToLocalStorage('notifications', notifications);
        }

        // Refrescar vistas inmediatamente
        if (type === 'entrada') renderDocumentosTable();
        else renderSalidaDocumentosTable();
        updateHomeStats();

        // Feedback visual al usuario
        Swal.fire({
          toast: true,
          position: 'top-end',
          icon: 'success',
          title: 'Estado actualizado',
          showConfirmButton: false,
          timer: 2000
        });
      }
    }

    // --- Gestión de Organigrama ---
    const btnOpenOrganigrama = document.getElementById('btnOpenOrganigrama');
    if (btnOpenOrganigrama) {
      btnOpenOrganigrama.addEventListener('click', function() {
        renderOrganigrama();
        const modal = bootstrap.Modal.getOrCreateInstance(document.getElementById('organigramaModal'));
        modal.show();
      });
    }

    function renderOrganigrama() {
      const container = document.getElementById('organigramaContainer');
      const controls = document.getElementById('adminStructureControls');
      
      if (!container) return;

      // Mostrar/ocultar controles de edición solo para administradores
      if (currentUser && currentUser.role === 'admin') {
        controls.classList.remove('d-none');
      } else {
        controls.classList.add('d-none');
      }

      let html = '<div class="row g-4">';
      
      for (const division in divisionToDepartments) {
        const departments = divisionToDepartments[division];
        const divisionName = getDivisionDisplay(division);
        
        html += `
          <div class="col-md-6 col-lg-4">
            <div class="card h-100 border-primary shadow-sm" style="background: rgba(255,255,255,0.9)">
              <div class="card-header bg-primary text-white d-flex justify-content-between align-items-center">
                <h6 class="mb-0 text-truncate" title="${divisionName}">${divisionName}</h6>
                ${currentUser && currentUser.role === 'admin' ? 
                  `<button class="btn btn-sm btn-outline-light border-0" onclick="removeDivision('${division}')"><i class="fas fa-trash"></i></button>` : ''}
              </div>
              <div class="card-body">
                <ul class="list-group list-group-flush">
                  ${departments.length > 0 ? departments.map((dep, idx) => `
                    <li class="list-group-item d-flex justify-content-between align-items-center py-2 px-0 bg-transparent">
                      <span class="small">${getDepartamentoDisplay(dep)}</span>
                      ${currentUser && currentUser.role === 'admin' ? 
                        `<button class="btn btn-sm text-danger p-0 border-0 bg-transparent" onclick="removeDepartment('${division}', ${idx})"><i class="fas fa-times-circle"></i></button>` : ''}
                    </li>
                  `).join('') : '<li class="list-group-item text-muted small italic px-0 bg-transparent">Sin departamentos</li>'}
                </ul>
                ${currentUser && currentUser.role === 'admin' ? `
                  <div class="mt-3">
                    <label class="small text-muted mb-1">Nuevo Departamento:</label>
                    <div class="input-group input-group-sm">
                      <input type="text" id="newDepInput_${division}" class="form-control" placeholder="Nombre de departamento">
                      <button class="btn btn-success" onclick="addDepartmentInline('${division}')">
                        <i class="fas fa-plus"></i>
                      </button>
                    </div>
                  </div>
                ` : ''}
              </div>
            </div>
          </div>
        `;
      }
      
      html += '</div>';
      container.innerHTML = html;
    }

    window.addDivisionStructure = function() {
      const input = document.getElementById('newDivisionName');
      const name = input.value.trim();
      if (!name) return;
      
      const key = name.toLowerCase().replace(/\s+/g, '_').normalize("NFD").replace(/[\u0300-\u036f]/g, "");
      if (divisionToDepartments[key]) {
        Swal.fire('Error', 'Esta división ya existe', 'error');
        return;
      }
      
      divisionToDepartments[key] = [];
      saveStructure();
      input.value = '';
      Swal.fire('Éxito', 'División añadida correctamente', 'success');
    };

    window.addDepartmentInline = function(division) {
      const input = document.getElementById(`newDepInput_${division}`);
      if (!input) return;
      const depName = input.value.trim();
      if (!depName) return;
      
      const depKey = depName.toLowerCase().replace(/\s+/g, '_').normalize("NFD").replace(/[\u0300-\u036f]/g, "");
      if (divisionToDepartments[division].includes(depKey)) {
        Swal.fire('Error', 'Este departamento ya existe en esta división', 'error');
        return;
      }
      
      divisionToDepartments[division].push(depKey);
      saveStructure();
      input.value = '';

      Swal.fire({
        toast: true,
        position: 'top-end',
        icon: 'success',
        title: 'Departamento añadido',
        showConfirmButton: false,
        timer: 2000
      });
    };

    window.removeDivision = function(division) {
      Swal.fire({
        title: '¿Eliminar División?',
        text: `Se borrará "${getDivisionDisplay(division)}" y todos sus departamentos asociados.`,
        icon: 'warning',
        showCancelButton: true,
        confirmButtonColor: '#d33',
        confirmButtonText: 'Sí, eliminar',
        cancelButtonText: 'Cancelar'
      }).then((result) => {
        if (result.isConfirmed) {
          delete divisionToDepartments[division];
          saveStructure();
        }
      });
    };

    window.removeDepartment = function(division, index) {
      divisionToDepartments[division].splice(index, 1);
      saveStructure();
    };

    function saveStructure() {
      localStorage.setItem('systemStructure', JSON.stringify(divisionToDepartments));
      renderOrganigrama();
      populateUserSelects(); // Mantiene sincronizados los select de creación de usuarios
    }

    // Initialize
    SecurityFirewall.init();
    renderActionCheckboxes('accion', 'ent');
    renderActionCheckboxes('salidaAccion', 'sal');
    renderActionCheckboxes('editAccion', 'edt');

    // Defensive init: wrap calls that depend on DOM elements
    try {
      initUserAssignmentContainers();
    } catch (e) {
      console.warn('initUserAssignmentContainers fallo:', e);
    }

    try { updateUsersTable(); } catch (e) { console.warn('updateUsersTable fallo:', e); }
    try { updateHomeStats(); } catch (e) { console.warn('updateHomeStats fallo:', e); }
    try { renderDocumentosTable(); } catch (e) { console.warn('renderDocumentosTable fallo:', e); }
    try { renderSalidaDocumentosTable(); } catch (e) { console.warn('renderSalidaDocumentosTable fallo:', e); }
    try { populateUserSelects(); } catch (e) { console.warn('populateUserSelects fallo:', e); }

    // Restore session if active
    if (currentUser) {
      // Validate user against database to ensure permissions are up to date
      const validUser = users.find(u => u.username === currentUser.username);
      
      if (validUser) {
        // Update session with latest user data
        // Ensure division is copied to currentUser
        currentUser = validUser;
        sessionStorage.setItem('currentUser', JSON.stringify(currentUser));
        
        document.getElementById('appContent').classList.add('show');
        updateUI();
        
        // Restore last active section
        const lastSection = sessionStorage.getItem('lastActiveSection') || 'homeSection';
        showSection(lastSection);
      } else {
        // User invalid or deleted
        currentUser = null;
        sessionStorage.removeItem('currentUser');
        document.getElementById('loginScreen').classList.remove('d-none');
      }
    } else {
      document.getElementById('loginScreen').classList.remove('d-none');
    }
    // Load external app.js if present (safe fallback)
    try {
      const script = document.createElement('script');
      script.src = 'app.js';
      script.defer = true;
      document.body.appendChild(script);
    } catch (e) {
      console.warn('No se pudo cargar app.js:', e);
    }
  </script>
</body>
</html>
