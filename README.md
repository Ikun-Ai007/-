[index.html](https://github.com/user-attachments/files/31496257/index.html)# -
云服务科技企业服务平台（以下简称“平台”）是为中小企业提供的一站式云资源管理中枢，集成了资源开通、工单处理、成本分析、安全审计、健康诊断、方案商城等核心模块。本说明书旨在从架构、数据、功能三个维度阐述平台的设计思路，帮助开发团队和运维人员快速理解系统的内在逻辑。
[Uploading index.html…正在上传 index.html…]()
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>云服务科技企业服务平台 V1.0</title>
    <style>
        /* ===== CSS Variables & Reset ===== *//* ===== CSS 变量与重置 ===== */
        :root {
            --primary: #2563eb;
            --primary-light: #3b82f6;
            --primary-dark: #1d4ed8;
            --primary-bg: #eff6ff;
            --success: #16a34a;
            --success-bg: #dcfce7;
            --warning: #f59e0b;
            --warning-bg: #fef3c7;
            --danger: #dc2626;
            --danger-bg: #fee2e2;
            --gray-50: #f8fafc;
            --gray-100: #f1f5f9;
            --gray-200: #e2e8f0;
            --gray-300: #cbd5e1;
            --gray-400: #94a3b8;
            --gray-500: #64748b;
            --gray-600: #475569;
            --gray-700: #334155;
            --gray-800: #1e293b;
            --gray-900: #0f172a;
            --radius: 12px;
            --shadow: 0 4px 24px rgba(0, 0, 0, 0.06);
            --shadow-hover: 0 8px 32px rgba(0, 0, 0, 0.10);
            --transition: 0.25s ease;
            --sidebar-width: 240px;
            --header-height: 60px;
            --font: 'Segoe UI', system-ui, -apple-system, sans-serif;
        }

        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        body {
            font-family: var(--font);
            background: var(--gray-50);
            color: var(--gray-800);
            height: 100vh;
            overflow: hidden;
            display: flex;
        }

        /* ===== Scrollbar ===== */
        ::-webkit-scrollbar {
            width: 5px;
            height: 5px;
        }
        ::-webkit-scrollbar-track {
            background: transparent;
        }
        ::-webkit-scrollbar-thumb {
            background: var(--gray-300);
            border-radius: 10px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: var(--gray-400);
        }

        /* ===== Login Overlay ===== */
        #login-overlay {
            position: fixed;
            inset: 0;
            z-index: 9999;
            background: rgba(15, 23, 42, 0.75);
            backdrop-filter: blur(12px);
            display: flex;
            align-items: center;
            justify-content: center;
            transition: opacity 0.5s ease, visibility 0.5s ease;
        }
        #login-overlay.hidden {
            opacity: 0;
            visibility: hidden;
            pointer-events: none;
        }
        .login-box {
            background: #fff;
            border-radius: var(--radius);
            padding: 48px 40px 40px;
            width: 400px;
            max-width: 92vw;
            box-shadow: 0 24px 80px rgba(0, 0, 0, 0.3);
            text-align: center;
            animation: fadeUp 0.5s ease;
        }
        @keyframes fadeUp {
            from {
                opacity: 0;
                transform: translateY(30px) scale(0.96);
            }
            to {
                opacity: 1;
                transform: translateY(0) scale(1);
            }
        }
        .login-box .logo {
            font-size: 28px;
            font-weight: 700;
            color: var(--primary);
            letter-spacing: -0.5px;
            margin-bottom: 6px;
        }
        .login-box .sub {
            color: var(--gray-500);
            font-size: 14px;
            margin-bottom: 28px;
        }
        .login-box .field {
            text-align: left;
            margin-bottom: 16px;
        }
        .login-box .field label {
            display: block;
            font-size: 13px;
            font-weight: 600;
            color: var(--gray-700);
            margin-bottom: 4px;
        }
        .login-box .field input {
            width: 100%;
            padding: 10px 14px;
            border: 1.5px solid var(--gray-200);
            border-radius: 8px;
            font-size: 14px;
            transition: border 0.2s;
            outline: none;
        }
        .login-box .field input:focus {
            border-color: var(--primary);
            box-shadow: 0 0 0 3px rgba(37, 99, 235, 0.12);
        }
        .login-box .btn-login {
            width: 100%;
            padding: 12px;
            background: var(--primary);
            color: #fff;
            border: none;
            border-radius: 8px;
            font-size: 16px;
            font-weight: 600;
            cursor: pointer;
            transition: background 0.2s;
            margin-top: 8px;
        }
        .login-box .btn-login:hover {
            background: var(--primary-dark);
        }
        .login-box .hint {
            font-size: 13px;
            color: var(--gray-400);
            margin-top: 14px;
        }
        .login-box .hint span {
            color: var(--gray-600);
            font-weight: 500;
        }

        /* ===== Sidebar ===== */
        .sidebar {
            width: var(--sidebar-width);
            height: 100vh;
            background: #fff;
            border-right: 1px solid var(--gray-200);
            display: flex;
            flex-direction: column;
            flex-shrink: 0;
            position: sticky;
            top: 0;
            overflow-y: auto;
            padding: 20px 0 20px 16px;
            z-index: 10;
        }
        .sidebar .brand {
            font-size: 20px;
            font-weight: 700;
            color: var(--gray-900);
            padding: 0 12px 24px 12px;
            display: flex;
            align-items: center;
            gap: 8px;
            border-bottom: 1px solid var(--gray-100);
        }
        .sidebar .brand .badge {
            font-size: 10px;
            background: var(--primary-bg);
            color: var(--primary);
            padding: 2px 8px;
            border-radius: 20px;
            font-weight: 600;
        }
        .sidebar .nav {
            flex: 1;
            padding-top: 16px;
        }
        .sidebar .nav-item {
            display: flex;
            align-items: center;
            gap: 10px;
            padding: 10px 12px;
            border-radius: 8px;
            cursor: pointer;
            transition: background 0.2s, color 0.2s;
            color: var(--gray-600);
            font-size: 14px;
            font-weight: 500;
            margin-bottom: 2px;
            border: none;
            background: transparent;
            width: calc(100% - 8px);
            text-align: left;
        }
        .sidebar .nav-item:hover {
            background: var(--gray-100);
            color: var(--gray-900);
        }
        .sidebar .nav-item.active {
            background: var(--primary-bg);
            color: var(--primary);
            font-weight: 600;
        }
        .sidebar .nav-item .icon {
            font-size: 18px;
            width: 28px;
            text-align: center;
            flex-shrink: 0;
        }
        .sidebar .nav-item .label {
            white-space: nowrap;
        }
        .sidebar .footer {
            padding: 12px 12px 0;
            font-size: 12px;
            color: var(--gray-400);
            border-top: 1px solid var(--gray-100);
            margin-top: 8px;
        }

        /* ===== Main Content ===== */
        .main {
            flex: 1;
            display: flex;
            flex-direction: column;
            height: 100vh;
            overflow: hidden;
            background: var(--gray-50);
        }
        .header {
            height: var(--header-height);
            background: #fff;
            border-bottom: 1px solid var(--gray-200);
            display: flex;
            align-items: center;
            justify-content: space-between;
            padding: 0 28px;
            flex-shrink: 0;
        }
        .header .page-title {
            font-size: 18px;
            font-weight: 600;
            color: var(--gray-900);
        }
        .header .user {
            display: flex;
            align-items: center;
            gap: 16px;
            font-size: 14px;
            color: var(--gray-600);
        }
        .header .user .avatar {
            width: 34px;
            height: 34px;
            border-radius: 50%;
            background: var(--primary-bg);
            color: var(--primary);
            display: flex;
            align-items: center;
            justify-content: center;
            font-weight: 600;
            font-size: 14px;
        }
        .content {
            flex: 1;
            overflow-y: auto;
            padding: 24px 28px 32px;
        }

        /* ===== Views ===== */
        .view {
            display: none;
            animation: fadeIn 0.3s ease;
        }
        .view.active {
            display: block;
        }
        @keyframes fadeIn {
            from {
                opacity: 0;
                transform: translateY(6px);
            }
            to {
                opacity: 1;
                transform: translateY(0);
            }
        }

        /* ===== Dashboard ===== */
        .stats-grid {
            display: grid;
            grid-template-columns: repeat(5, 1fr);
            gap: 16px;
            margin-bottom: 24px;
        }
        .stat-card {
            background: #fff;
            border-radius: var(--radius);
            padding: 18px 20px;
            box-shadow: var(--shadow);
            border: 1px solid var(--gray-100);
            transition: transform 0.2s, box-shadow 0.2s;
            cursor: pointer;
        }
        .stat-card:hover {
            transform: translateY(-2px);
            box-shadow: var(--shadow-hover);
            border-color: var(--primary-light);
        }
        .stat-card .num {
            font-size: 28px;
            font-weight: 700;
            color: var(--gray-900);
            line-height: 1.2;
        }
        .stat-card .label {
            font-size: 13px;
            color: var(--gray-500);
            margin-top: 2px;
        }
        .stat-card .trend {
            font-size: 12px;
            font-weight: 600;
            margin-top: 4px;
        }
        .stat-card .trend.up {
            color: var(--success);
        }
        .stat-card .trend.down {
            color: var(--danger);
        }

        .dash-row {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 20px;
            margin-bottom: 20px;
        }
        .dash-row-2 {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 20px;
        }
        .panel {
            background: #fff;
            border-radius: var(--radius);
            padding: 20px;
            box-shadow: var(--shadow);
            border: 1px solid var(--gray-100);
        }
        .panel .panel-title {
            font-size: 15px;
            font-weight: 600;
            color: var(--gray-800);
            margin-bottom: 14px;
            display: flex;
            align-items: center;
            justify-content: space-between;
        }
        .panel .panel-title .more {
            font-size: 12px;
            color: var(--gray-400);
            font-weight: 400;
            cursor: pointer;
        }
        .panel .panel-title .more:hover {
            color: var(--primary);
        }

        /* Health Ring */
        .ring-wrap {
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 32px;
            padding: 4px 0;
        }
        .ring-canvas {
            width: 160px;
            height: 160px;
        }
        .ring-legend {
            display: flex;
            flex-direction: column;
            gap: 6px;
            font-size: 13px;
            color: var(--gray-600);
        }
        .ring-legend .dot {
            display: inline-block;
            width: 12px;
            height: 12px;
            border-radius: 4px;
            margin-right: 8px;
        }

        /* Bar chart */
        .bar-chart {
            display: flex;
            align-items: flex-end;
            justify-content: space-around;
            height: 140px;
            padding-top: 10px;
            gap: 12px;
        }
        .bar-item {
            display: flex;
            flex-direction: column;
            align-items: center;
            flex: 1;
        }
        .bar-item .bar {
            width: 100%;
            max-width: 36px;
            border-radius: 4px 4px 0 0;
            background: var(--primary-light);
            transition: height 0.6s ease;
            min-height: 4px;
        }
        .bar-item .bar-label {
            font-size: 11px;
            color: var(--gray-500);
            margin-top: 6px;
            white-space: nowrap;
        }
        .bar-item .bar-val {
            font-size: 11px;
            font-weight: 600;
            color: var(--gray-700);
            margin-bottom: 4px;
        }

        /* Ticket feed */
        .ticket-feed {
            display: flex;
            flex-direction: column;
            gap: 8px;
        }
        .ticket-feed .item {
            display: flex;
            align-items: center;
            justify-content: space-between;
            padding: 8px 12px;
            background: var(--gray-50);
            border-radius: 6px;
            font-size: 13px;
            border-left: 3px solid var(--gray-300);
        }
        .ticket-feed .item .tag {
            font-size: 11px;
            padding: 2px 8px;
            border-radius: 12px;
            font-weight: 500;
        }
        .ticket-feed .item .tag.pending {
            background: var(--warning-bg);
            color: #b45309;
        }
        .ticket-feed .item .tag.done {
            background: var(--success-bg);
            color: #166534;
        }
        .ticket-feed .item .tag.progress {
            background: var(--primary-bg);
            color: var(--primary);
        }

        /* Resource status bars */
        .status-bars {
            display: flex;
            flex-direction: column;
            gap: 8px;
        }
        .status-bars .row {
            display: flex;
            align-items: center;
            gap: 10px;
            font-size: 13px;
        }
        .status-bars .row .bar-bg {
            flex: 1;
            height: 10px;
            background: var(--gray-100);
            border-radius: 20px;
            overflow: hidden;
        }
        .status-bars .row .bar-bg .fill {
            height: 100%;
            border-radius: 20px;
            transition: width 0.8s ease;
        }

        /* ===== Resource / Ticket / Event Tables ===== */
        .toolbar {
            display: flex;
            flex-wrap: wrap;
            align-items: center;
            gap: 12px;
            margin-bottom: 16px;
        }
        .toolbar .group {
            display: flex;
            align-items: center;
            gap: 8px;
            flex-wrap: wrap;
        }
        .toolbar input,
        .toolbar select {
            padding: 6px 12px;
            border: 1.5px solid var(--gray-200);
            border-radius: 8px;
            font-size: 13px;
            outline: none;
            background: #fff;
            transition: border 0.2s;
        }
        .toolbar input:focus,
        .toolbar select:focus {
            border-color: var(--primary);
        }
        .toolbar .btn {
            padding: 6px 16px;
            border: none;
            border-radius: 8px;
            font-size: 13px;
            font-weight: 500;
            cursor: pointer;
            transition: background 0.2s, transform 0.1s;
            background: var(--gray-100);
            color: var(--gray-700);
        }
        .toolbar .btn:hover {
            background: var(--gray-200);
        }
        .toolbar .btn.primary {
            background: var(--primary);
            color: #fff;
        }
        .toolbar .btn.primary:hover {
            background: var(--primary-dark);
        }
        .toolbar .btn.success {
            background: var(--success);
            color: #fff;
        }
        .toolbar .btn.success:hover {
            background: #15803d;
        }
        .toolbar .btn.danger {
            background: var(--danger);
            color: #fff;
        }
        .toolbar .btn.danger:hover {
            background: #b91c1c;
        }
        .toolbar .btn.warning {
            background: var(--warning);
            color: #fff;
        }
        .toolbar .btn.warning:hover {
            background: #d97706;
        }
        .toolbar .btn:active {
            transform: scale(0.96);
        }

        .table-wrap {
            overflow-x: auto;
            border-radius: var(--radius);
            border: 1px solid var(--gray-200);
            background: #fff;
        }
        table {
            width: 100%;
            border-collapse: collapse;
            font-size: 13px;
        }
        table th {
            background: var(--gray-50);
            text-align: left;
            padding: 10px 14px;
            font-weight: 600;
            color: var(--gray-600);
            border-bottom: 1px solid var(--gray-200);
            white-space: nowrap;
        }
        table td {
            padding: 10px 14px;
            border-bottom: 1px solid var(--gray-100);
            color: var(--gray-700);
        }
        table tr:hover td {
            background: var(--gray-50);
        }
        table tr.selected td {
            background: var(--primary-bg);
        }
        .status-badge {
            display: inline-block;
            padding: 2px 10px;
            border-radius: 20px;
            font-size: 12px;
            font-weight: 500;
        }
        .status-badge.running {
            background: var(--success-bg);
            color: #166534;
        }
        .status-badge.stopped {
            background: var(--gray-200);
            color: var(--gray-600);
        }
        .status-badge.maintenance {
            background: var(--warning-bg);
            color: #b45309;
        }
        .status-badge.alarm {
            background: var(--danger-bg);
            color: #b91c1c;
        }
        .status-badge.pending {
            background: var(--warning-bg);
            color: #b45309;
        }
        .status-badge.progress {
            background: var(--primary-bg);
            color: var(--primary);
        }
        .status-badge.resolved {
            background: var(--success-bg);
            color: #166534;
        }
        .status-badge.closed {
            background: var(--gray-200);
            color: var(--gray-600);
        }

        /* Energy bar */
        .energy-bar {
            width: 70px;
            height: 8px;
            background: var(--gray-200);
            border-radius: 20px;
            overflow: hidden;
            display: inline-block;
            vertical-align: middle;
        }
        .energy-bar .fill {
            height: 100%;
            border-radius: 20px;
            transition: width 0.5s ease;
        }

        /* Detail panel */
        .detail-panel {
            background: #fff;
            border-radius: var(--radius);
            padding: 18px 20px;
            border: 1px solid var(--gray-200);
            margin-top: 16px;
        }
        .detail-panel .title {
            font-weight: 600;
            font-size: 15px;
            margin-bottom: 10px;
        }
        .detail-panel .grid {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 4px 24px;
            font-size: 13px;
        }
        .detail-panel .grid .item {
            display: flex;
            padding: 4px 0;
            border-bottom: 1px solid var(--gray-50);
        }
        .detail-panel .grid .item .k {
            color: var(--gray-500);
            width: 80px;
            flex-shrink: 0;
        }
        .detail-panel .grid .item .v {
            color: var(--gray-800);
        }
        .detail-panel .actions {
            margin-top: 14px;
            display: flex;
            gap: 10px;
            flex-wrap: wrap;
        }

        /* ===== Knowledge Cards ===== */
        .knowledge-grid {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(220px, 1fr));
            gap: 16px;
            margin-top: 12px;
        }
        .knowledge-card {
            background: #fff;
            border-radius: var(--radius);
            padding: 16px 18px;
            border: 1px solid var(--gray-200);
            box-shadow: var(--shadow);
            cursor: pointer;
            transition: transform 0.2s, box-shadow 0.2s;
        }
        .knowledge-card:hover {
            transform: translateY(-3px);
            box-shadow: var(--shadow-hover);
        }
        .knowledge-card .tag {
            font-size: 11px;
            background: var(--primary-bg);
            color: var(--primary);
            padding: 2px 10px;
            border-radius: 20px;
            display: inline-block;
            margin-bottom: 6px;
        }
        .knowledge-card .title {
            font-weight: 600;
            font-size: 15px;
            margin-bottom: 4px;
        }
        .knowledge-card .desc {
            font-size: 13px;
            color: var(--gray-500);
            display: -webkit-box;
            -webkit-line-clamp: 2;
            -webkit-box-orient: vertical;
            overflow: hidden;
        }
        .knowledge-card .meta {
            font-size: 12px;
            color: var(--gray-400);
            margin-top: 8px;
        }

        /* ===== Solution Cards ===== */
        .solution-grid {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
            gap: 16px;
            margin-top: 12px;
        }
        .solution-card {
            background: #fff;
            border-radius: var(--radius);
            padding: 16px 18px;
            border: 1px solid var(--gray-200);
            box-shadow: var(--shadow);
            cursor: pointer;
            transition: transform 0.2s, box-shadow 0.2s;
        }
        .solution-card:hover {
            transform: translateY(-3px);
            box-shadow: var(--shadow-hover);
        }
        .solution-card .cat {
            font-size: 11px;
            color: var(--gray-400);
            text-transform: uppercase;
            letter-spacing: 0.5px;
        }
        .solution-card .name {
            font-weight: 600;
            font-size: 15px;
            margin: 4px 0 2px;
        }
        .solution-card .desc {
            font-size: 13px;
            color: var(--gray-500);
            display: -webkit-box;
            -webkit-line-clamp: 2;
            -webkit-box-orient: vertical;
            overflow: hidden;
        }
        .solution-card .price {
            font-weight: 700;
            color: var(--primary);
            margin-top: 6px;
        }
        .solution-card .rating {
            font-size: 13px;
            color: var(--warning);
        }

        /* ===== Health Diagnosis ===== */
        .diag-score {
            display: flex;
            align-items: center;
            gap: 32px;
            background: #fff;
            border-radius: var(--radius);
            padding: 20px 24px;
            border: 1px solid var(--gray-200);
            margin-bottom: 20px;
        }
        .diag-score .big {
            font-size: 48px;
            font-weight: 700;
            color: var(--primary);
        }
        .diag-score .info .label {
            font-size: 14px;
            color: var(--gray-500);
        }
        .diag-score .info .comment {
            font-size: 15px;
            font-weight: 500;
        }
        .diag-items {
            display: flex;
            flex-direction: column;
            gap: 8px;
        }
        .diag-item {
            display: flex;
            align-items: center;
            gap: 14px;
            padding: 10px 16px;
            background: #fff;
            border-radius: 8px;
            border: 1px solid var(--gray-200);
            font-size: 14px;
        }
        .diag-item .status {
            font-weight: 600;
            min-width: 50px;
        }
        .diag-item .status.pass {
            color: var(--success);
        }
        .diag-item .status.warn {
            color: var(--warning);
        }
        .diag-item .status.fail {
            color: var(--danger);
        }
        .diag-item .status.wait {
            color: var(--gray-400);
        }

        /* ===== Charter ===== */
        .charter-layout {
            display: grid;
            grid-template-columns: 220px 1fr;
            gap: 24px;
        }
        .charter-layout .arch {
            background: #fff;
            border-radius: var(--radius);
            padding: 20px;
            border: 1px solid var(--gray-200);
        }
        .charter-layout .arch .layer {
            padding: 10px 14px;
            border-left: 4px solid var(--primary);
            margin-bottom: 6px;
            background: var(--gray-50);
            border-radius: 0 6px 6px 0;
            font-size: 13px;
            font-weight: 500;
        }
        .charter-layout .arch .layer .sub {
            font-weight: 400;
            color: var(--gray-500);
            font-size: 12px;
        }
        .charter-layout .doc {
            background: #fff;
            border-radius: var(--radius);
            padding: 20px 24px;
            border: 1px solid var(--gray-200);
            max-height: 480px;
            overflow-y: auto;
        }
        .charter-layout .doc h4 {
            margin: 16px 0 6px;
            color: var(--gray-800);
        }
        .charter-layout .doc p,
        .charter-layout .doc li {
            font-size: 13px;
            color: var(--gray-600);
            line-height: 1.6;
        }
        .charter-layout .doc ul {
            padding-left: 20px;
        }

        /* ===== Modal ===== */
        .modal-overlay {
            position: fixed;
            inset: 0;
            z-index: 999;
            background: rgba(15, 23, 42, 0.5);
            backdrop-filter: blur(4px);
            display: none;
            align-items: center;
            justify-content: center;
        }
        .modal-overlay.open {
            display: flex;
        }
        .modal-box {
            background: #fff;
            border-radius: var(--radius);
            padding: 28px 32px;
            max-width: 520px;
            width: 92vw;
            max-height: 80vh;
            overflow-y: auto;
            box-shadow: 0 24px 80px rgba(0, 0, 0, 0.25);
            animation: fadeUp 0.3s ease;
        }
        .modal-box h3 {
            font-size: 18px;
            margin-bottom: 16px;
        }
        .modal-box .field {
            margin-bottom: 14px;
        }
        .modal-box .field label {
            display: block;
            font-size: 13px;
            font-weight: 500;
            color: var(--gray-700);
            margin-bottom: 3px;
        }
        .modal-box .field input,
        .modal-box .field select,
        .modal-box .field textarea {
            width: 100%;
            padding: 8px 12px;
            border: 1.5px solid var(--gray-200);
            border-radius: 8px;
            font-size: 13px;
            outline: none;
            transition: border 0.2s;
            font-family: inherit;
        }
        .modal-box .field input:focus,
        .modal-box .field select:focus,
        .modal-box .field textarea:focus {
            border-color: var(--primary);
        }
        .modal-box .field textarea {
            resize: vertical;
            min-height: 60px;
        }
        .modal-box .actions {
            display: flex;
            gap: 10px;
            justify-content: flex-end;
            margin-top: 18px;
        }
        .modal-box .actions .btn {
            padding: 8px 20px;
            border: none;
            border-radius: 8px;
            font-size: 14px;
            font-weight: 500;
            cursor: pointer;
            transition: background 0.2s;
        }
        .modal-box .actions .btn.cancel {
            background: var(--gray-100);
            color: var(--gray-600);
        }
        .modal-box .actions .btn.cancel:hover {
            background: var(--gray-200);
        }
        .modal-box .actions .btn.confirm {
            background: var(--primary);
            color: #fff;
        }
        .modal-box .actions .btn.confirm:hover {
            background: var(--primary-dark);
        }

        /* ===== Responsive ===== */
        @media (max-width: 1024px) {
            .stats-grid {
                grid-template-columns: repeat(3, 1fr);
            }
            .dash-row,
            .dash-row-2 {
                grid-template-columns: 1fr;
            }
            .charter-layout {
                grid-template-columns: 1fr;
            }
        }
        @media (max-width: 768px) {
            .sidebar {
                width: 64px;
                padding: 12px 8px;
            }
            .sidebar .brand {
                font-size: 14px;
                padding: 0 4px 16px 4px;
                justify-content: center;
            }
            .sidebar .brand .badge {
                display: none;
            }
            .sidebar .nav-item .label {
                display: none;
            }
            .sidebar .nav-item {
                justify-content: center;
                padding: 10px;
            }
            .sidebar .nav-item .icon {
                font-size: 20px;
                width: auto;
            }
            .sidebar .footer {
                display: none;
            }
            .stats-grid {
                grid-template-columns: repeat(2, 1fr);
            }
            .header {
                padding: 0 16px;
            }
            .content {
                padding: 16px;
            }
            .detail-panel .grid {
                grid-template-columns: 1fr;
            }
            .login-box {
                padding: 32px 20px;
            }
            .ring-wrap {
                flex-direction: column;
                gap: 16px;
            }
            .diag-score {
                flex-direction: column;
                gap: 12px;
                text-align: center;
            }
        }
        @media (max-width: 480px) {
            .stats-grid {
                grid-template-columns: 1fr 1fr;
                gap: 10px;
            }
            .stat-card {
                padding: 12px 14px;
            }
            .stat-card .num {
                font-size: 20px;
            }
            .toolbar {
                flex-direction: column;
                align-items: stretch;
            }
            .toolbar .group {
                flex-wrap: wrap;
            }
            .toolbar .group>* {
                flex: 1;
                min-width: 80px;
            }
        }

        /* Toast */
        .toast {
            position: fixed;
            bottom: 30px;
            right: 30px;
            background: var(--gray-900);
            color: #fff;
            padding: 12px 24px;
            border-radius: 10px;
            font-size: 14px;
            box-shadow: 0 8px 32px rgba(0, 0, 0, 0.2);
            z-index: 99999;
            opacity: 0;
            transform: translateY(20px);
            transition: opacity 0.3s, transform 0.3s;
            pointer-events: none;
        }
        .toast.show {
            opacity: 1;
            transform: translateY(0);
        }
    </style>
</head>
<body>

    <!-- ===== LOGIN OVERLAY ===== -->
    <div id="login-overlay">
        <div class="login-box">
            <div class="logo">☁️ 云服务科技企业服务平台</div>
            <div class="sub">一站式云资源管理中枢 · V1.0</div>
            <div class="field">
                <label>用户名</label>
                <input id="login-user" type="text" value="admin" />
            </div>
            <div class="field">
                <label>密码</label>
                <input id="login-pass" type="password" value="admin123" />
            </div>
            <button class="btn-login" onclick="doLogin()">登 录</button>
            <div class="hint">演示账号 <span>admin</span> ｜ 密码 <span>admin123</span></div>
        </div>
    </div>

    <!-- ===== SIDEBAR ===== -->
    <aside class="sidebar" id="sidebar">
        <div class="brand">
            ☁️ 云服务 <span class="badge">V1.0</span>
        </div>
        <nav class="nav" id="navMenu">
            <button class="nav-item active" data-view="dashboard"><span class="icon">📊</span><span class="label">总控舱</span></button>
            <button class="nav-item" data-view="resources"><span class="icon">🖥️</span><span class="label">资源开通</span></button>
            <button class="nav-item" data-view="cost"><span class="icon">💰</span><span class="label">成本透视</span></button>
            <button class="nav-item" data-view="tickets"><span class="icon">🎫</span><span class="label">工单中枢</span></button>
            <button class="nav-item" data-view="knowledge"><span class="icon">📚</span><span class="label">知识脉络</span></button>
            <button class="nav-item" data-view="health"><span class="icon">🩺</span><span class="label">健康诊断</span></button>
            <button class="nav-item" data-view="solutions"><span class="icon">🏪</span><span class="label">方案商城</span></button>
            <button class="nav-item" data-view="charter"><span class="icon">📜</span><span class="label">系统宪章</span></button>
            <button class="nav-item" data-view="security"><span class="icon">🛡️</span><span class="label">安全哨兵</span></button>
        </nav>
        <div class="footer">© 2026 云服务科技</div>
    </aside>

    <!-- ===== MAIN ===== -->
    <div class="main">
        <header class="header">
            <div class="page-title" id="pageTitle">总控舱</div>
            <div class="user">
                <span id="userName">admin</span>
                <div class="avatar">A</div>
            </div>
        </header>
        <div class="content" id="contentArea">

            <!-- ===== VIEW: Dashboard ===== -->
            <div class="view active" id="view-dashboard">
                <div class="stats-grid" id="dashStats"></div>
                <div class="dash-row">
                    <div class="panel">
                        <div class="panel-title">🔄 资源健康度 <span class="more">环形图</span></div>
                        <div class="ring-wrap">
                            <canvas id="healthRing" class="ring-canvas" width="160" height="160"></canvas>
                            <div class="ring-legend" id="ringLegend"></div>
                        </div>
                    </div>
                    <div class="panel">
                        <div class="panel-title">📊 部门成本对比 <span class="more">柱状图</span></div>
                        <div class="bar-chart" id="costBarChart"></div>
                    </div>
                </div>
                <div class="dash-row-2">
                    <div class="panel">
                        <div class="panel-title">📋 最新工单动态 <span class="more">实时</span></div>
                        <div class="ticket-feed" id="ticketFeed"></div>
                    </div>
                    <div class="panel">
                        <div class="panel-title">📈 资源状态分布 <span class="more">概览</span></div>
                        <div class="status-bars" id="statusBars"></div>
                    </div>
                </div>
                <div style="margin-top:20px;text-align:right;">
                    <button class="btn primary" onclick="runSmartCheck()">🔍 智能巡检</button>
                </div>
            </div>

            <!-- ===== VIEW: Resources ===== -->
            <div class="view" id="view-resources">
                <div class="toolbar">
                    <div class="group">
                        <span style="font-size:13px;color:var(--gray-500);">状态：</span>
                        <select id="resFilterStatus" onchange="renderResources()">
                            <option value="all">全部</option>
                            <option value="运行中">运行中</option>
                            <option value="告警">告警</option>
                            <option value="维护中">维护中</option>
                            <option value="已停止">已停止</option>
                        </select>
                    </div>
                    <div class="group">
                        <input id="resSearch" placeholder="搜索名称/IP/部门..." oninput="renderResources()" />
                    </div>
                    <div class="group" style="margin-left:auto;">
                        <button class="btn primary" onclick="showNewResource()">➕ 新建申请</button>
                        <button class="btn success" onclick="importCSV()">📥 导入清单</button>
                        <button class="btn" onclick="exportCSV()">📤 导出清单</button>
                    </div>
                </div>
                <div style="display:grid;grid-template-columns:1fr 320px;gap:16px;">
                    <div class="table-wrap">
                        <table>
                            <thead><tr><th>名称</th><th>类型</th><th>区域</th><th>状态</th><th>IP</th><th>费用</th><th>部门</th></tr></thead>
                            <tbody id="resTableBody"></tbody>
                        </table>
                    </div>
                    <div class="detail-panel" id="resDetail">
                        <div class="title">📌 资源详情</div>
                        <div style="color:var(--gray-400);font-size:13px;padding:12px 0;">← 点击表格行查看详情</div>
                    </div>
                </div>
            </div>

            <!-- ===== VIEW: Cost ===== -->
            <div class="view" id="view-cost">
                <div class="toolbar">
                    <div class="group">
                        <span style="font-size:13px;color:var(--gray-500);">月份：</span>
                        <select id="costMonth" onchange="renderCost()">
                            <option value="2024-01">2024-01</option>
                            <option value="2024-02">2024-02</option>
                            <option value="2024-03" selected>2024-03</option>
                        </select>
                    </div>
                    <div style="margin-left:auto;">
                        <button class="btn" onclick="exportCost()">📤 导出部门费用</button>
                    </div>
                </div>
                <div class="stats-grid" id="costStats"></div>
                <div style="display:grid;grid-template-columns:1fr 1fr;gap:16px;margin:16px 0;">
                    <div class="panel"><div class="panel-title">📋 部门汇总</div><div class="table-wrap" style="border:none;"><table><thead><tr><th>部门</th><th>费用</th><th>预算</th><th>偏差</th></tr></thead><tbody id="costDeptTable"></tbody></table></div></div>
                    <div class="panel"><div class="panel-title">📊 服务类型费用</div><div class="table-wrap" style="border:none;"><table><thead><tr><th>服务类型</th><th>费用</th><th>占比</th></tr></thead><tbody id="costSvcTable"></tbody></table></div></div>
                </div>
                <div class="panel"><div class="panel-title">📊 部门费用柱状图</div><div class="bar-chart" id="costBarChart2" style="height:160px;"></div></div>
            </div>

            <!-- ===== VIEW: Tickets ===== -->
            <div class="view" id="view-tickets">
                <div class="toolbar">
                    <div class="group">
                        <span style="font-size:13px;color:var(--gray-500);">状态：</span>
                        <select id="ticketFilter" onchange="renderTickets()">
                            <option value="all">全部</option>
                            <option value="待处理">待处理</option>
                            <option value="处理中">处理中</option>
                            <option value="已解决">已解决</option>
                            <option value="已关闭">已关闭</option>
                            <option value="我负责的">我负责的</option>
                        </select>
                    </div>
                    <div style="margin-left:auto;">
                        <button class="btn primary" onclick="showNewTicket()">➕ 创建工单</button>
                    </div>
                </div>
                <div style="display:grid;grid-template-columns:1fr 320px;gap:16px;">
                    <div class="table-wrap">
                        <table>
                            <thead><tr><th>工单号</th><th>标题</th><th>优先级</th><th>状态</th><th>指派人</th><th>能量</th></tr></thead>
                            <tbody id="ticketTableBody"></tbody>
                        </table>
                    </div>
                    <div class="detail-panel" id="ticketDetail">
                        <div class="title">🎫 工单详情</div>
                        <div style="color:var(--gray-400);font-size:13px;padding:12px 0;">← 点击表格行查看详情</div>
                    </div>
                </div>
            </div>

            <!-- ===== VIEW: Knowledge ===== -->
            <div class="view" id="view-knowledge">
                <div class="toolbar">
                    <div class="group">
                        <input id="knowSearch" placeholder="搜索知识标题..." oninput="renderKnowledge()" style="width:240px;" />
                    </div>
                </div>
                <div class="knowledge-grid" id="knowledgeGrid"></div>
            </div>

            <!-- ===== VIEW: Health ===== -->
            <div class="view" id="view-health">
                <div style="margin-bottom:16px;">
                    <button class="btn primary" onclick="runDiagnosis()">🔬 执行全面诊断</button>
                </div>
                <div class="diag-score" id="diagScore">
                    <div class="big" id="diagBig">--</div>
                    <div class="info">
                        <div class="label">综合评分</div>
                        <div class="comment" id="diagComment">点击「执行全面诊断」开始检查</div>
                    </div>
                </div>
                <div class="diag-items" id="diagItems"></div>
            </div>

            <!-- ===== VIEW: Solutions ===== -->
            <div class="view" id="view-solutions">
                <div class="toolbar">
                    <div class="group">
                        <span style="font-size:13px;color:var(--gray-500);">分类：</span>
                        <select id="solCat" onchange="renderSolutions()">
                            <option value="all">全部</option>
                            <option value="计算优化">计算优化</option>
                            <option value="数据服务">数据服务</option>
                            <option value="安全合规">安全合规</option>
                            <option value="运维自动化">运维自动化</option>
                            <option value="AI/大数据">AI/大数据</option>
                        </select>
                    </div>
                    <div class="group">
                        <input id="solSearch" placeholder="搜索方案..." oninput="renderSolutions()" style="width:200px;" />
                    </div>
                </div>
                <div class="solution-grid" id="solutionGrid"></div>
            </div>

            <!-- ===== VIEW: Charter ===== -->
            <div class="view" id="view-charter">
                <div class="charter-layout">
                    <div class="arch">
                        <div style="font-weight:600;margin-bottom:12px;font-size:14px;">🏛️ 系统分层架构</div>
                        <div class="layer">接入层 <span class="sub">· 登录/网关</span></div>
                        <div class="layer">服务层 <span class="sub">· 业务逻辑</span></div>
                        <div class="layer">数据层 <span class="sub">· SQLite</span></div>
                        <div class="layer">安全层 <span class="sub">· 审计/加密</span></div>
                        <div class="layer">运维层 <span class="sub">· 监控/日志</span></div>
                    </div>
                    <div class="doc" id="charterDoc">
                        <h3>📜 系统宪章</h3>
                        <h4>SLA 服务等级</h4>
                        <p>平台承诺年度可用性 ≥ 99.9%，故障响应时间 ≤ 15 分钟，由运维团队 7×24 小时值守。</p>
                        <h4>数据安全承诺</h4>
                        <p>所有用户数据采用 AES-256 加密存储，传输使用 TLS 1.3 协议。定期进行安全审计与渗透测试。</p>
                        <h4>平台更新日志</h4>
                        <ul>
                            <li><strong>V1.0</strong> (2026-08) — 初始版本，包含总控舱、资源、成本、工单、知识、诊断、方案、宪章、安全九大模块。</li>
                            <li><strong>V0.9</strong> (2026-07) — 内部测试版，核心功能验证。</li>
                        </ul>
                        <h4>用户权限说明</h4>
                        <p>系统内置管理员账号，拥有全部操作权限。普通用户可查看与提交，但无法删除或修改系统配置。</p>
                        <p style="margin-top:12px;color:var(--gray-400);font-size:12px;">* 本文档纯展示，内容为模拟说明。</p>
                    </div>
                </div>
            </div>

            <!-- ===== VIEW: Security ===== -->
            <div class="view" id="view-security">
                <div class="toolbar">
                    <div class="group">
                        <span style="font-size:13px;color:var(--gray-500);">级别：</span>
                        <select id="secFilter" onchange="renderSecurity()">
                            <option value="all">全部</option>
                            <option value="严重">严重</option>
                            <option value="高危">高危</option>
                            <option value="中危">中危</option>
                            <option value="低危">低危</option>
                            <option value="信息">信息</option>
                        </select>
                    </div>
                    <div style="margin-left:auto;">
                        <button class="btn warning" onclick="runSecurityScan()">🛡️ 发起安全扫描</button>
                    </div>
                </div>
                <div class="stats-grid" id="secStats"></div>
                <div style="display:grid;grid-template-columns:1fr 320px;gap:16px;margin-top:12px;">
                    <div class="table-wrap">
                        <table>
                            <thead><tr><th>事件ID</th><th>时间</th><th>类型</th><th>级别</th><th>来源</th></tr></thead>
                            <tbody id="secTableBody"></tbody>
                        </table>
                    </div>
                    <div class="detail-panel" id="secDetail">
                        <div class="title">🛡️ 事件详情</div>
                        <div style="color:var(--gray-400);font-size:13px;padding:12px 0;">← 点击表格行查看详情</div>
                    </div>
                </div>
            </div>

        </div><!-- /content -->
    </div><!-- /main -->

    <!-- ===== MODAL ===== -->
    <div class="modal-overlay" id="modalOverlay">
        <div class="modal-box" id="modalBox">
            <h3 id="modalTitle">标题</h3>
            <div id="modalBody"></div>
            <div class="actions">
                <button class="btn cancel" onclick="closeModal()">取消</button>
                <button class="btn confirm" id="modalConfirm" onclick="modalConfirm()">确认</button>
            </div>
        </div>
    </div>

    <!-- ===== TOAST ===== -->
    <div class="toast" id="toast"></div>

    <script>
        // ============================================================
        // DATA
        // ============================================================
        const DATA = {
            resources: [
                { id: 'RES-2024-0001', name: 'WH-APP-01', type: '云服务器', region: '武汉', status: '运行中', ip: '10.0.1.10',
                    cpu: 32, mem: 58, disk: 42, cost: 1890, dept: '研发部', created: '2024-01-15', spec: '4核8G' },
                { id: 'RES-2024-0002', name: 'BJ-DB-02', type: '数据库', region: '北京', status: '运行中', ip: '10.0.2.20',
                    cpu: 18, mem: 64, disk: 71, cost: 3200, dept: '运维部', created: '2024-02-01', spec: '2核4G' },
                { id: 'RES-2024-0003', name: 'SH-CDN-03', type: '对象存储', region: '上海', status: '告警', ip: '10.0.3.30',
                    cpu: 82, mem: 76, disk: 88, cost: 560, dept: '市场部', created: '2024-02-20', spec: '标准型' },
                { id: 'RES-2024-0004', name: 'GZ-CACHE-04', type: '缓存服务', region: '广州', status: '维护中', ip: '10.0.4.40',
                    cpu: 12, mem: 22, disk: 15, cost: 780, dept: '研发部', created: '2024-03-01', spec: '1核2G' },
                { id: 'RES-2024-0005', name: 'WH-BACKUP-05', type: '云服务器', region: '武汉', status: '已停止', ip: '10.0.5.50',
                    cpu: 0, mem: 0, disk: 0, cost: 0, dept: '运维部', created: '2024-03-10', spec: '2核4G' },
                { id: 'RES-2024-0006', name: 'BJ-MONITOR-06', type: '云服务器', region: '北京', status: '运行中', ip: '10.0.6.60',
                    cpu: 45, mem: 52, disk: 33, cost: 1200, dept: '运维部', created: '2024-03-15', spec: '4核8G' },
                { id: 'RES-2024-0007', name: 'SH-BIGDATA-07', type: '大数据', region: '上海', status: '告警', ip: '10.0.7.70',
                    cpu: 91, mem: 83, disk: 94, cost: 4500, dept: '数据部', created: '2024-04-01', spec: '8核16G' },
                { id: 'RES-2024-0008', name: 'GZ-APP-08', type: '云服务器', region: '广州', status: '运行中', ip: '10.0.8.80',
                    cpu: 28, mem: 41, disk: 29, cost: 960, dept: '研发部', created: '2024-04-10', spec: '2核4G' },
            ],
            tickets: [
                { id: 'TK-2024-0001', title: 'API 响应超时', priority: '非常紧急', status: '待处理', assignee: '张三', dept: '运维部',
                    res: 'WH-APP-01', created: '2024-08-26 09:30', updated: '2024-08-26 09:30', energy: 92 },
                { id: 'TK-2024-0002', title: '磁盘空间不足告警', priority: '紧急', status: '处理中', assignee: '李四', dept: '运维部',
                    res: 'BJ-DB-02', created: '2024-08-25 16:20', updated: '2024-08-26 08:10', energy: 68 },
                { id: 'TK-2024-0003', title: '新员工账号开通', priority: '一般', status: '已解决', assignee: '王五', dept: '人事部',
                    res: '-', created: '2024-08-24 11:00', updated: '2024-08-25 09:00', energy: 45 },
                { id: 'TK-2024-0004', title: '数据库连接池耗尽', priority: '紧急', status: '待处理', assignee: '赵六', dept: '研发部',
                    res: 'SH-BIGDATA-07', created: '2024-08-26 10:15', updated: '2024-08-26 10:15', energy: 85 },
                { id: 'TK-2024-0005', title: '安全组规则变更', priority: '普通', status: '已关闭', assignee: '张三', dept: '运维部',
                    res: 'WH-APP-01', created: '2024-08-23 14:30', updated: '2024-08-24 16:00', energy: 20 },
                { id: 'TK-2024-0006', title: '月结账单核对', priority: '一般', status: '处理中', assignee: '李四', dept: '财务部',
                    res: '-', created: '2024-08-22 09:00', updated: '2024-08-23 10:30', energy: 55 },
            ],
            costs: [
                { dept: '研发部', svc: '云服务器', month: '2024-03', amount: 2850, budget: 3000 },
                { dept: '研发部', svc: '缓存服务', month: '2024-03', amount: 780, budget: 800 },
                { dept: '运维部', svc: '数据库', month: '2024-03', amount: 3200, budget: 2800 },
                { dept: '运维部', svc: '云服务器', month: '2024-03', amount: 1200, budget: 1500 },
                { dept: '市场部', svc: '对象存储', month: '2024-03', amount: 560, budget: 600 },
                { dept: '数据部', svc: '大数据', month: '2024-03', amount: 4500, budget: 4000 },
                { dept: '研发部', svc: '云服务器', month: '2024-02', amount: 2600, budget: 3000 },
                { dept: '运维部', svc: '数据库', month: '2024-02', amount: 3100, budget: 2800 },
                { dept: '市场部', svc: '对象存储', month: '2024-02', amount: 520, budget: 600 },
                { dept: '数据部', svc: '大数据', month: '2024-02', amount: 4200, budget: 4000 },
                { dept: '研发部', svc: '云服务器', month: '2024-01', amount: 2400, budget: 3000 },
                { dept: '运维部', svc: '数据库', month: '2024-01', amount: 2900, budget: 2800 },
                { dept: '市场部', svc: '对象存储', month: '2024-01', amount: 480, budget: 600 },
                { dept: '数据部', svc: '大数据', month: '2024-01', amount: 3900, budget: 4000 },
            ],
            solutions: [
                { id: 'SOL-2024-001', name: '智能弹性伸缩', cat: '计算优化', price: 0, vendor: '自研', desc: '基于负载自动调整实例数量，节省30%成本。',
                    rating: 4.8, users: 156 },
                { id: 'SOL-2024-002', name: '跨云灾备方案', cat: '数据服务', price: 1200, vendor: '华为云联营', desc: '异地容灾，RPO≤15分钟，RTO≤2小时。',
                    rating: 4.6, users: 89 },
                { id: 'SOL-2024-003', name: '安全态势感知', cat: '安全合规', price: 800, vendor: '自研', desc: '实时威胁检测与智能告警，覆盖OWASP Top10。',
                    rating: 4.9, users: 203 },
                { id: 'SOL-2024-004', name: '自动化运维工具链', cat: '运维自动化', price: 0, vendor: '开源集成', desc: 'Ansible + Jenkins 流水线，一键部署。',
                    rating: 4.5, users: 312 },
                { id: 'SOL-2024-005', name: 'AI 智能日志分析', cat: 'AI/大数据', price: 1500, vendor: '阿里云联营', desc: '机器学习日志异常检测，秒级定位故障。',
                    rating: 4.7, users: 67 },
                { id: 'SOL-2024-006', name: '数据加密与密钥管理', cat: '安全合规', price: 600, vendor: '自研', desc: '端到端加密，密钥轮转与审计。',
                    rating: 4.4, users: 128 },
            ],
            events: [
                { id: 'EVT-0001', time: '2024-08-26 08:12:34', kind: '安全告警', level: '严重', source: '武汉-控制台',
                    detail: '检测到异常SSH登录尝试，来源IP 45.33.22.11', handled: 0 },
                { id: 'EVT-0002', time: '2024-08-25 23:45:01', kind: '登录审计', level: '信息', source: '北京-控制台',
                    detail: '用户 admin 于 23:45 登录成功', handled: 0 },
                { id: 'EVT-0003', time: '2024-08-25 14:20:18', kind: '安全告警', level: '高危', source: '上海-控制台',
                    detail: 'API 密钥疑似泄露，已触发自动轮转', handled: 0 },
                { id: 'EVT-0004', time: '2024-08-24 09:33:07', kind: '操作审计', level: '低危', source: '广州-控制台',
                    detail: '用户 李四 删除未使用资源 WH-TEST-01', handled: 1 },
                { id: 'EVT-0005', time: '2024-08-23 16:08:44', kind: '安全告警', level: '中危', source: '武汉-控制台',
                    detail: '防火墙规则变更未通过审批', handled: 0 },
                { id: 'EVT-0006', time: '2024-08-23 10:00:00', kind: '系统事件', level: '信息', source: '系统',
                    detail: '平台版本 V1.0 正式发布', handled: 0 },
            ],
            knowledge: [
                { id: 'K-001', cat: '运维最佳实践', title: '云资源标签管理规范', desc: '统一标签体系，提升资源可见性与成本分摊准确性。',
                author: '张工', date: '2024-08-20' },
                { id: 'K-002', cat: '故障排查', title: '常见网络延迟问题定位', desc: '从客户端到服务端的全链路排查方法与工具链。',
                    author: '李工', date: '2024-08-18' },
                { id: 'K-003', cat: '安全合规', title: '数据加密与访问控制策略', desc: '分级加密、最小权限原则与审计日志配置指南。',
                    author: '王工', date: '2024-08-15' },
                { id: 'K-004', cat: '成本优化', title: '按需 vs 包年包月选型对比', desc: '结合业务负载特征，选择最经济的计费模式。',
                    author: '赵工', date: '2024-08-12' },
                { id: 'K-005', cat: '自动化', title: 'CI/CD 流水线集成云资源', desc: '通过 Terraform 实现基础设施即代码，自动化部署。',
                    author: '孙工', date: '2024-08-10' },
                { id: 'K-006', cat: '监控告警', title: 'Prometheus + Grafana 监控体系搭建', desc: '从数据采集到可视化告警的完整实践。',
                    author: '周工', date: '2024-08-08' },
            ],
            selectedResource: null,
            selectedTicket: null,
            selectedEvent: null,
            diagRunning: false,
            diagResults: [],
        };

        // ============================================================
        // HELPERS
        // ============================================================
        function toast(msg) {
            const el = document.getElementById('toast');
            el.textContent = msg;
            el.classList.add('show');
            clearTimeout(el._timer);
            el._timer = setTimeout(() => el.classList.remove('show'), 2600);
        }

        function genId(prefix) {
            const n = String(Date.now()).slice(-6);
            return prefix + '-' + n;
        }

        function rand(min, max) { return Math.floor(Math.random() * (max - min + 1)) + min; }

        function statusClass(status) {
            const map = { '运行中': 'running', '已停止': 'stopped', '维护中': 'maintenance', '告警': 'alarm',
                '待处理': 'pending', '处理中': 'progress', '已解决': 'resolved', '已关闭': 'closed' };
            return map[status] || '';
        }

        function modalOpen(title, bodyHtml, confirmText, onConfirm) {
            document.getElementById('modalTitle').textContent = title;
            document.getElementById('modalBody').innerHTML = bodyHtml;
            document.getElementById('modalConfirm').textContent = confirmText || '确认';
            document.getElementById('modalOverlay').classList.add('open');
            window._modalConfirm = onConfirm || null;
        }

        function closeModal() {
            document.getElementById('modalOverlay').classList.remove('open');
            window._modalConfirm = null;
        }

        function modalConfirm() {
            if (typeof window._modalConfirm === 'function') {
                window._modalConfirm();
            }
            closeModal();
        }
        // click outside to close
        document.getElementById('modalOverlay').addEventListener('click', function(e) {
            if (e.target === this) closeModal();
        });

        // ============================================================
        // LOGIN
        // ============================================================
        function doLogin() {
            const user = document.getElementById('login-user').value.trim();
            const pass = document.getElementById('login-pass').value.trim();
            if (user === 'admin' && pass === 'admin123') {
                document.getElementById('login-overlay').classList.add('hidden');
                document.getElementById('userName').textContent = user;
                toast('登录成功，欢迎回来！');
                initApp();
            } else {
                toast('用户名或密码错误，请重试。');
            }
        }
        document.addEventListener('keydown', function(e) {
            if (e.key === 'Enter' && !document.getElementById('login-overlay').classList.contains('hidden')) {
                doLogin();
            }
        });

        // ============================================================
        // NAVIGATION
        // ============================================================
        const viewTitles = {
            dashboard: '总控舱',
            resources: '资源开通',
            cost: '成本透视',
            tickets: '工单中枢',
            knowledge: '知识脉络',
            health: '健康诊断',
            solutions: '方案商城',
            charter: '系统宪章',
            security: '安全哨兵'
        };

        document.querySelectorAll('.nav-item').forEach(btn => {
            btn.addEventListener('click', function() {
                document.querySelectorAll('.nav-item').forEach(b => b.classList.remove('active'));
                this.classList.add('active');
                const view = this.dataset.view;
                document.querySelectorAll('.view').forEach(v => v.classList.remove('active'));
                document.getElementById('view-' + view).classList.add('active');
                document.getElementById('pageTitle').textContent = viewTitles[view] || view;
                // trigger render
                if (view === 'dashboard') renderDashboard();
                if (view === 'resources') renderResources();
                if (view === 'cost') renderCost();
                if (view === 'tickets') renderTickets();
                if (view === 'knowledge') renderKnowledge();
                if (view === 'health') renderDiagnosis();
                if (view === 'solutions') renderSolutions();
                if (view === 'security') renderSecurity();
                if (view === 'charter') { /* static */ }
            });
        });

        // ============================================================
        // DASHBOARD
        // ============================================================
        function renderDashboard() {
            const res = DATA.resources;
            const tickets = DATA.tickets;
            const total = res.length;
            const running = res.filter(r => r.status === '运行中').length;
            const alarm = res.filter(r => r.status === '告警').length;
            const pending = tickets.filter(t => t.status === '待处理' || t.status === '处理中').length;
            const totalCost = res.reduce((s, r) => s + r.cost, 0);

            document.getElementById('dashStats').innerHTML = `
            <div class="stat-card" onclick="navigateTo('resources')"><div class="num">${total}</div><div class="label">资源总数</div></div>
            <div class="stat-card" onclick="navigateTo('resources')"><div class="num">${running}</div><div class="label">运行中</div><div class="trend up">↑ 稳定</div></div>
            <div class="stat-card" onclick="navigateTo('resources')"><div class="num">${alarm}</div><div class="label">告警</div><div class="trend down">⚠ 需关注</div></div>
            <div class="stat-card" onclick="navigateTo('tickets')"><div class="num">${pending}</div><div class="label">待处理工单</div></div>
            <div class="stat-card" onclick="navigateTo('cost')"><div class="num">¥${totalCost.toLocaleString()}</div><div class="label">本月费用</div></div>
          `;

            // Health Ring
            drawHealthRing();
            // Cost Bar Chart
            drawCostBarChart('costBarChart', getDeptCosts());
            // Ticket feed
            const feed = tickets.slice(0, 6);
            document.getElementById('ticketFeed').innerHTML = feed.map(t => `
            <div class="item" style="border-left-color:${t.priority==='非常紧急'?'#dc2626':t.priority==='紧急'?'#f59e0b':'#3b82f6'}">
              <span>${t.title}</span>
              <span><span class="tag ${statusClass(t.status)}">${t.status}</span></span>
            </div>
          `).join('');

            // Status bars
            const counts = { '运行中': 0, '告警': 0, '维护中': 0, '已停止': 0 };
            res.forEach(r => { if (counts[r.status] !== undefined) counts[r.status]++; });
            const colors = { '运行中': '#16a34a', '告警': '#dc2626', '维护中': '#f59e0b', '已停止': '#94a3b8' };
            document.getElementById('statusBars').innerHTML = Object.entries(counts).map(([k, v]) => `
            <div class="row">
              <span style="width:56px;">${k}</span>
              <div class="bar-bg"><div class="fill" style="width:${total? (v/total*100).toFixed(0):0}%;background:${colors[k]};"></div></div>
              <span style="min-width:32px;text-align:right;">${v}</span>
            </div>
          `).join('');
        }

        function getDeptCosts() {
            const map = {};
            DATA.resources.forEach(r => { map[r.dept] = (map[r.dept] || 0) + r.cost; });
            return Object.entries(map).sort((a, b) => b[1] - a[1]);
        }

        function drawCostBarChart(id, data) {
            const el = document.getElementById(id);
            if (!el) return;
            const max = Math.max(...data.map(d => d[1]), 1);
            el.innerHTML = data.map(([dept, val]) => `
            <div class="bar-item">
              <div class="bar-val">${val>0?'¥'+val:''}</div>
              <div class="bar" style="height:${Math.max(4, (val/max)*100)}%;background:#3b82f6;"></div>
              <div class="bar-label">${dept}</div>
            </div>
          `).join('');
        }

        function drawHealthRing() {
            const canvas = document.getElementById('healthRing');
            const ctx = canvas.getContext('2d');
            const w = 160,
                h = 160,
                cx = 80,
                cy = 80,
                r = 62,
                lw = 16;
            const data = [
                { label: '计算', val: 78, color: '#3b82f6' },
                { label: '存储', val: 65, color: '#8b5cf6' },
                { label: '网络', val: 92, color: '#06b6d4' },
                { label: '安全', val: 70, color: '#f59e0b' },
            ];
            ctx.clearRect(0, 0, w, h);
            let start = -Math.PI / 2;
            data.forEach(d => {
                const end = start + (d.val / 100) * Math.PI * 2;
                ctx.beginPath();
                ctx.arc(cx, cy, r, start, end);
                ctx.strokeStyle = d.color;
                ctx.lineWidth = lw;
                ctx.lineCap = 'round';
                ctx.stroke();
                start = end + 0.03;
            });
            // center text
            ctx.fillStyle = '#1e293b';
            ctx.font = 'bold 16px sans-serif';
            ctx.textAlign = 'center';
            ctx.textBaseline = 'middle';
            ctx.fillText('健康度', cx, cy - 6);
            ctx.fillStyle = '#64748b';
            ctx.font = '12px sans-serif';
            ctx.fillText('综合 76%', cx, cy + 16);

            // legend
            document.getElementById('ringLegend').innerHTML = data.map(d =>
                `<div><span class="dot" style="background:${d.color};"></span>${d.label} ${d.val}%</div>`
            ).join('');
        }

        function navigateTo(view) {
            document.querySelector(`.nav-item[data-view="${view}"]`)?.click();
        }

        function runSmartCheck() {
            toast('🔍 智能巡检启动... 正在扫描资源与工单状态');
            setTimeout(() => {
                const total = DATA.resources.length;
                const alarm = DATA.resources.filter(r => r.status === '告警').length;
                const pending = DATA.tickets.filter(t => t.status === '待处理' || t.status === '处理中').length;
                const score = Math.max(60, 100 - alarm * 8 - pending * 4 + rand(0, 10));
                const grade = score >= 85 ? '优秀' : score >= 70 ? '良好' : '需改进';
                toast(`🔍 巡检完成：综合评分 ${score} 分 (${grade})，告警 ${alarm} 个，待处理工单 ${pending} 个`);
                modalOpen('📋 智能巡检报告', `
              <div style="font-size:14px;line-height:1.8;">
                <p><strong>检查时间：</strong>${new Date().toLocaleString()}</p>
                <p><strong>资源总数：</strong>${total}</p>
                <p><strong>告警资源：</strong>${alarm} ${alarm>0?'⚠️ 请关注':''}</p>
                <p><strong>待处理工单：</strong>${pending}</p>
                <p><strong>综合评分：</strong><span style="font-size:24px;font-weight:700;color:${score>=80?'#16a34a':score>=60?'#f59e0b':'#dc2626'};">${score}</span> 分</p>
                <p><strong>改进建议：</strong>${score>=85?'系统运行良好，继续保持。':score>=70?'建议关注告警资源，优化工单处理效率。':'请立即处理告警资源，并清理积压工单。'}</p>
              </div>
            `, '关闭', null);
            }, 600);
        }

        // ============================================================
        // RESOURCES
        // ============================================================
        function renderResources() {
            const status = document.getElementById('resFilterStatus').value;
            const search = document.getElementById('resSearch').value.toLowerCase();
            let list = DATA.resources.filter(r => {
                if (status !== 'all' && r.status !== status) return false;
                if (search && !r.name.toLowerCase().includes(search) && !r.ip.includes(search) && !r.dept.toLowerCase()
                    .includes(search)) return false;
                return true;
            });
            const tbody = document.getElementById('resTableBody');
            tbody.innerHTML = list.map((r, i) => `
            <tr data-idx="${i}" onclick="selectResource(${i})" class="${DATA.selectedResource===i?'selected':''}">
              <td><strong>${r.name}</strong></td>
              <td>${r.type}</td>
              <td>${r.region}</td>
              <td><span class="status-badge ${statusClass(r.status)}">${r.status}</span></td>
              <td>${r.ip}</td>
              <td>¥${r.cost}</td>
              <td>${r.dept}</td>
            </tr>
          `).join('');
            // if selected index out of range
            if (DATA.selectedResource !== null && DATA.selectedResource >= list.length) {
                DATA.selectedResource = null;
            }
            if (DATA.selectedResource !== null && list.length > 0) {
                showResourceDetail(DATA.selectedResource, list);
            } else if (list.length > 0) {
                selectResource(0);
            } else {
                document.getElementById('resDetail').innerHTML =
                    `<div class="title">📌 资源详情</div><div style="color:var(--gray-400);font-size:13px;padding:12px 0;">无匹配资源</div>`;
            }
        }

        function selectResource(idx) {
            const list = getFilteredResources();
            if (idx >= list.length) return;
            DATA.selectedResource = idx;
            showResourceDetail(idx, list);
            renderResources(); // refresh highlight
        }

        function getFilteredResources() {
            const status = document.getElementById('resFilterStatus').value;
            const search = document.getElementById('resSearch').value.toLowerCase();
            return DATA.resources.filter(r => {
                if (status !== 'all' && r.status !== status) return false;
                if (search && !r.name.toLowerCase().includes(search) && !r.ip.includes(search) && !r.dept.toLowerCase()
                    .includes(search)) return false;
                return true;
            });
        }

        function showResourceDetail(idx, list) {
            const r = list[idx];
            const el = document.getElementById('resDetail');
            const canStop = r.status === '运行中';
            const canDel = r.status === '已停止';
            el.innerHTML = `
            <div class="title">📌 ${r.name}</div>
            <div class="grid">
              <div class="item"><span class="k">ID</span><span class="v">${r.id}</span></div>
              <div class="item"><span class="k">类型</span><span class="v">${r.type}</span></div>
              <div class="item"><span class="k">区域</span><span class="v">${r.region}</span></div>
              <div class="item"><span class="k">IP</span><span class="v">${r.ip}</span></div>
              <div class="item"><span class="k">规格</span><span class="v">${r.spec}</span></div>
              <div class="item"><span class="k">CPU</span><span class="v">${r.cpu}%</span></div>
              <div class="item"><span class="k">内存</span><span class="v">${r.mem}%</span></div>
              <div class="item"><span class="k">磁盘</span><span class="v">${r.disk}%</span></div>
              <div class="item"><span class="k">费用</span><span class="v">¥${r.cost}</span></div>
              <div class="item"><span class="k">部门</span><span class="v">${r.dept}</span></div>
              <div class="item"><span class="k">创建日期</span><span class="v">${r.created}</span></div>
              <div class="item"><span class="k">状态</span><span class="v"><span class="status-badge ${statusClass(r.status)}">${r.status}</span></span></div>
            </div>
            <div class="actions">
              ${canStop?`<button class="btn warning" onclick="stopResource('${r.id}')">⏹ 停止资源</button>`:''}
              ${canDel?`<button class="btn danger" onclick="deleteResource('${r.id}')">🗑 删除资源</button>`:''}
            </div>
          `;
        }

        function stopResource(id) {
            const r = DATA.resources.find(x => x.id === id);
            if (r && r.status === '运行中') {
                r.status = '已停止';
                r.cpu = 0;
                r.mem = 0;
                r.disk = 0;
                toast(`⏹ 资源 ${r.name} 已停止`);
                renderResources();
                renderDashboard();
            }
        }

        function deleteResource(id) {
            const idx = DATA.resources.findIndex(x => x.id === id);
            if (idx > -1) {
                const name = DATA.resources[idx].name;
                DATA.resources.splice(idx, 1);
                DATA.selectedResource = null;
                toast(`🗑 资源 ${name} 已删除`);
                renderResources();
                renderDashboard();
            }
        }

        function showNewResource() {
            modalOpen('📝 新建资源申请', `
            <div class="field"><label>类型</label><select id="nrType"><option>云服务器</option><option>数据库</option><option>对象存储</option><option>缓存服务</option><option>大数据</option></select></div>
            <div class="field"><label>区域</label><select id="nrRegion"><option>武汉</option><option>北京</option><option>上海</option><option>广州</option></select></div>
            <div class="field"><label>规格</label><input id="nrSpec" value="4核8G" /></div>
            <div class="field"><label>部门</label><select id="nrDept"><option>研发部</option><option>运维部</option><option>市场部</option><option>数据部</option><option>财务部</option></select></div>
            <div class="field"><label>费用（元/月）</label><input id="nrCost" type="number" value="1200" /></div>
          `, '提交申请', function() {
                const type = document.getElementById('nrType').value;
                const region = document.getElementById('nrRegion').value;
                const spec = document.getElementById('nrSpec').value || '标准';
                const dept = document.getElementById('nrDept').value;
                const cost = parseFloat(document.getElementById('nrCost').value) || 0;
                const id = genId('RES');
                const name = region.slice(0, 2).toUpperCase() + '-' + type.slice(0, 4).toUpperCase() + '-' + String(
                DATA.resources.length + 1).padStart(2, '0');
                DATA.resources.push({
                    id,
                    name,
                    type,
                    region,
                    status: '运行中',
                    ip: '10.0.' + rand(1, 9) + '.' + rand(10, 99),
                    cpu: rand(10, 60),
                    mem: rand(20, 70),
                    disk: rand(10, 50),
                    cost,
                    dept,
                    created: new Date().toISOString().slice(0, 10),
                    spec
                });
                toast('✅ 资源申请已提交，已自动创建');
                renderResources();
                renderDashboard();
            });
        }

        function importCSV() {
            toast('📥 导入功能模拟：已解析 CSV 并预览确认');
            // Simulate adding a resource
            const id = genId('RES');
            DATA.resources.push({
                id,
                name: 'IMP-TEST-01',
                type: '云服务器',
                region: '武汉',
                status: '运行中',
                ip: '10.0.9.99',
                cpu: 22,
                mem: 44,
                disk: 30,
                cost: 850,
                dept: '研发部',
                created: new Date().toISOString().slice(0, 10),
                spec: '2核4G'
            });
            renderResources();
            renderDashboard();
        }

        function exportCSV() {
            const list = getFilteredResources();
            let csv = '名称,类型,区域,状态,IP,费用,部门\n';
            list.forEach(r => { csv +=
                    `${r.name},${r.type},${r.region},${r.status},${r.ip},${r.cost},${r.dept}\n`; });
            downloadCSV(csv, '资源清单.csv');
            toast('📤 导出成功');
        }

        // ============================================================
        // COST
        // ============================================================
        function renderCost() {
            const month = document.getElementById('costMonth').value;
            const items = DATA.costs.filter(c => c.month === month);
            const deptMap = {};
            const svcMap = {};
            let total = 0;
            items.forEach(c => {
                deptMap[c.dept] = (deptMap[c.dept] || 0) + c.amount;
                svcMap[c.svc] = (svcMap[c.svc] || 0) + c.amount;
                total += c.amount;
            });
            const deptBudget = {};
            items.forEach(c => { deptBudget[c.dept] = (deptBudget[c.dept] || 0) + c.budget; });
            const overBudget = Object.keys(deptMap).filter(d => (deptMap[d] || 0) > (deptBudget[d] || 0)).length;
            const avg = Object.keys(deptMap).length ? (total / Object.keys(deptMap).length) : 0;

            document.getElementById('costStats').innerHTML = `
            <div class="stat-card"><div class="num">¥${total.toLocaleString()}</div><div class="label">总费用</div></div>
            <div class="stat-card"><div class="num">¥${Object.values(deptBudget).reduce((s,v)=>s+v,0).toLocaleString()}</div><div class="label">预算总额</div></div>
            <div class="stat-card"><div class="num">${overBudget}</div><div class="label">超预算部门数</div></div>
            <div class="stat-card"><div class="num">¥${avg.toFixed(0)}</div><div class="label">部门均摊</div></div>
          `;

            // Dept table
            document.getElementById('costDeptTable').innerHTML = Object.keys(deptMap).map(d => {
                const actual = deptMap[d];
                const budget = deptBudget[d] || 0;
                const diff = actual - budget;
                const sign = diff > 0 ? '🔺' : diff < 0 ? '🔻' : '▪';
                const color = diff > 0 ? 'var(--danger)' : diff < 0 ? 'var(--success)' : 'var(--gray-400)';
                return `<tr><td><strong>${d}</strong></td><td>¥${actual}</td><td>¥${budget}</td><td style="color:${color};">${sign} ${diff>0?'+':''}${diff.toFixed(0)}</td></tr>`;
            }).join('');

            // Svc table
            const svcTotal = Object.values(svcMap).reduce((s, v) => s + v, 0) || 1;
            document.getElementById('costSvcTable').innerHTML = Object.entries(svcMap).map(([k, v]) => `
            <tr><td>${k}</td><td>¥${v}</td><td>${(v/svcTotal*100).toFixed(1)}%</td></tr>
          `).join('');

            // Bar chart
            const sorted = Object.entries(deptMap).sort((a, b) => b[1] - a[1]);
            drawCostBarChart('costBarChart2', sorted);
        }

        function exportCost() {
            const month = document.getElementById('costMonth').value;
            const items = DATA.costs.filter(c => c.month === month);
            let csv = '部门,服务类型,费用,预算\n';
            items.forEach(c => { csv += `${c.dept},${c.svc},${c.amount},${c.budget}\n`; });
            downloadCSV(csv, `部门费用_${month}.csv`);
            toast('📤 部门费用已导出');
        }

        // ============================================================
        // TICKETS
        // ============================================================
        function renderTickets() {
            const filter = document.getElementById('ticketFilter').value;
            let list = DATA.tickets;
            if (filter === '我负责的') {
                list = list.filter(t => t.assignee === '张三');
            } else if (filter !== 'all') {
                list = list.filter(t => t.status === filter);
            }
            const tbody = document.getElementById('ticketTableBody');
            tbody.innerHTML = list.map((t, i) => `
            <tr data-idx="${i}" onclick="selectTicket(${i})" class="${DATA.selectedTicket===i?'selected':''}">
              <td><span style="font-weight:500;">${t.id}</span></td>
              <td>${t.title}</td>
              <td>${t.priority}</td>
              <td><span class="status-badge ${statusClass(t.status)}">${t.status}</span></td>
              <td>${t.assignee}</td>
              <td><div class="energy-bar"><div class="fill" style="width:${t.energy}%;background:${t.energy>70?'#dc2626':t.energy>40?'#f59e0b':'#3b82f6'};"></div></div> ${t.energy}</td>
            </tr>
          `).join('');
            if (DATA.selectedTicket !== null && DATA.selectedTicket >= list.length) DATA.selectedTicket = null;
            if (DATA.selectedTicket !== null && list.length > 0) {
                showTicketDetail(DATA.selectedTicket, list);
            } else if (list.length > 0) {
                selectTicket(0);
            } else {
                document.getElementById('ticketDetail').innerHTML =
                    `<div class="title">🎫 工单详情</div><div style="color:var(--gray-400);font-size:13px;padding:12px 0;">无工单</div>`;
            }
        }

        function selectTicket(idx) {
            const list = getFilteredTickets();
            if (idx >= list.length) return;
            DATA.selectedTicket = idx;
            showTicketDetail(idx, list);
            renderTickets();
        }

        function getFilteredTickets() {
            const filter = document.getElementById('ticketFilter').value;
            let list = DATA.tickets;
            if (filter === '我负责的') list = list.filter(t => t.assignee === '张三');
            else if (filter !== 'all') list = list.filter(t => t.status === filter);
            return list;
        }

        function showTicketDetail(idx, list) {
            const t = list[idx];
            const el = document.getElementById('ticketDetail');
            const canSolve = t.status === '待处理' || t.status === '处理中';
            const canClose = t.status === '已解决';
            el.innerHTML = `
            <div class="title">🎫 ${t.id} - ${t.title}</div>
            <div class="grid">
              <div class="item"><span class="k">优先级</span><span class="v">${t.priority}</span></div>
              <div class="item"><span class="k">状态</span><span class="v"><span class="status-badge ${statusClass(t.status)}">${t.status}</span></span></div>
              <div class="item"><span class="k">指派人</span><span class="v">${t.assignee}</span></div>
              <div class="item"><span class="k">部门</span><span class="v">${t.dept}</span></div>
              <div class="item"><span class="k">关联资源</span><span class="v">${t.res}</span></div>
              <div class="item"><span class="k">创建时间</span><span class="v">${t.created}</span></div>
              <div class="item"><span class="k">更新时间</span><span class="v">${t.updated}</span></div>
              <div class="item"><span class="k">能量值</span><span class="v">${t.energy}</span></div>
            </div>
            <div class="actions">
              ${canSolve?`<button class="btn success" onclick="solveTicket('${t.id}')">✅ 标记已解决</button>`:''}
              ${canClose?`<button class="btn" onclick="closeTicket('${t.id}')">🔒 关闭工单</button>`:''}
            </div>
          `;
        }

        function solveTicket(id) {
            const t = DATA.tickets.find(x => x.id === id);
            if (t && (t.status === '待处理' || t.status === '处理中')) {
                t.status = '已解决';
                t.updated = new Date().toLocaleString();
                toast(`✅ 工单 ${t.id} 已标记为已解决`);
                renderTickets();
                renderDashboard();
            }
        }

        function closeTicket(id) {
            const t = DATA.tickets.find(x => x.id === id);
            if (t && t.status === '已解决') {
                t.status = '已关闭';
                t.updated = new Date().toLocaleString();
                toast(`🔒 工单 ${t.id} 已关闭`);
                renderTickets();
                renderDashboard();
            }
        }

        function showNewTicket() {
            const depts = [...new Set(DATA.resources.map(r => r.dept))];
            const assignees = ['张三', '李四', '王五', '赵六'];
            const resources = DATA.resources.map(r => r.name);
            modalOpen('📝 创建工单', `
            <div class="field"><label>标题</label><input id="ntTitle" placeholder="请输入工单标题" /></div>
            <div class="field"><label>优先级</label><select id="ntPriority"><option>普通</option><option>一般</option><option>紧急</option><option>非常紧急</option></select></div>
            <div class="field"><label>指派人</label><select id="ntAssignee">${assignees.map(a=>`<option>${a}</option>`).join('')}</select></div>
            <div class="field"><label>部门</label><select id="ntDept">${depts.map(d=>`<option>${d}</option>`).join('')}</select></div>
            <div class="field"><label>关联资源</label><select id="ntRes"><option>-</option>${resources.map(r=>`<option>${r}</option>`).join('')}</select></div>
          `, '提交工单', function() {
                const title = document.getElementById('ntTitle').value.trim() || '未命名工单';
                const priority = document.getElementById('ntPriority').value;
                const assignee = document.getElementById('ntAssignee').value;
                const dept = document.getElementById('ntDept').value;
                const res = document.getElementById('ntRes').value;
                const id = genId('TK');
                DATA.tickets.unshift({
                    id,
                    title,
                    priority,
                    status: '待处理',
                    assignee,
                    dept,
                    res,
                    created: new Date().toLocaleString(),
                    updated: new Date().toLocaleString(),
                    energy: rand(50, 95)
                });
                toast('✅ 工单已创建');
                renderTickets();
                renderDashboard();
            });
        }

        // ============================================================
        // KNOWLEDGE
        // ============================================================
        function renderKnowledge() {
            const q = document.getElementById('knowSearch').value.toLowerCase();
            const list = DATA.knowledge.filter(k => k.title.toLowerCase().includes(q));
            document.getElementById('knowledgeGrid').innerHTML = list.map(k => `
            <div class="knowledge-card" onclick="showKnowledgeDetail('${k.id}')">
              <div class="tag">${k.cat}</div>
              <div class="title">${k.title}</div>
              <div class="desc">${k.desc}</div>
              <div class="meta">${k.author} · ${k.date}</div>
            </div>
          `).join('');
        }

        function showKnowledgeDetail(id) {
            const k = DATA.knowledge.find(x => x.id === id);
            if (!k) return;
            modalOpen('📖 ' + k.title, `
            <div style="font-size:14px;line-height:1.7;">
              <p><strong>分类：</strong>${k.cat}</p>
              <p><strong>作者：</strong>${k.author} · ${k.date}</p>
              <p style="margin-top:10px;">${k.desc}</p>
              <p style="margin-top:12px;color:var(--gray-500);font-size:13px;">这是一篇关于 ${k.cat} 的优质知识文章，点击「收藏」可保存到个人知识库。</p>
            </div>
          `, '⭐ 收藏', function() { toast('📚 已收藏到个人知识库'); });
        }

        // ============================================================
        // HEALTH DIAGNOSIS
        // ============================================================
        const DIAG_ITEMS = [
            { key: 'compute', label: '计算资源', desc: 'CPU 与内存使用率整体健康' },
            { key: 'storage', label: '存储容量', desc: '磁盘使用率正常，无容量瓶颈' },
            { key: 'network', label: '网络连通性', desc: '内网与外网连通性良好' },
            { key: 'database', label: '数据库健康', desc: '连接池及性能指标正常' },
            { key: 'security', label: '安全合规', desc: '无高危漏洞或违规配置' },
            { key: 'backup', label: '备份完整性', desc: '最近备份完整且可恢复' },
            { key: 'monitor', label: '监控告警', desc: '监控系统运行正常，无漏报' },
            { key: 'api', label: 'API 接口', desc: '核心接口响应时间 < 200ms' },
        ];

        function renderDiagnosis() {
            const items = document.getElementById('diagItems');
            if (DATA.diagResults.length === 0) {
                items.innerHTML = DIAG_ITEMS.map(d => `
              <div class="diag-item">
                <span style="font-weight:500;">${d.label}</span>
                <span class="status wait">⏳ 待检查</span>
                <span style="color:var(--gray-400);font-size:13px;">${d.desc}</span>
              </div>
            `).join('');
                document.getElementById('diagBig').textContent = '--';
                document.getElementById('diagComment').textContent = '点击「执行全面诊断」开始检查';
                return;
            }
            const results = DATA.diagResults;
            let pass = 0,
                warn = 0,
                fail = 0;
            items.innerHTML = DIAG_ITEMS.map((d, i) => {
                const r = results[i] || 'wait';
                if (r === 'pass') pass++;
                else if (r === 'warn') warn++;
                else if (r === 'fail') fail++;
                const statusMap = { pass: '✅ 通过', warn: '⚠️ 警告', fail: '❌ 异常', wait: '⏳ 待检查' };
                const clsMap = { pass: 'pass', warn: 'warn', fail: 'fail', wait: 'wait' };
                return `<div class="diag-item">
              <span style="font-weight:500;">${d.label}</span>
              <span class="status ${clsMap[r]}">${statusMap[r]}</span>
              <span style="color:var(--gray-500);font-size:13px;">${d.desc}</span>
            </div>`;
            }).join('');
            const score = Math.round(100 - (fail * 15 + warn * 5));
            const finalScore = Math.max(40, Math.min(100, score));
            document.getElementById('diagBig').textContent = finalScore;
            const comment = finalScore >= 85 ? '🎉 系统整体健康，运行稳定！' :
                finalScore >= 65 ? '⚠️ 部分指标需关注，建议优化。' :
                '🔴 存在异常项，请立即处理！';
            document.getElementById('diagComment').textContent = comment;
        }

        function runDiagnosis() {
            if (DATA.diagRunning) return;
            DATA.diagRunning = true;
            DATA.diagResults = [];
            const items = DIAG_ITEMS;
            let idx = 0;

            function step() {
                if (idx >= items.length) {
                    DATA.diagRunning = false;
                    renderDiagnosis();
                    toast('✅ 诊断完成');
                    return;
                }
                // random result with bias toward pass
                const rnd = Math.random();
                let result = 'pass';
                if (rnd < 0.15) result = 'fail';
                else if (rnd < 0.30) result = 'warn';
                DATA.diagResults.push(result);
                renderDiagnosis();
                idx++;
                setTimeout(step, 300 + rand(0, 300));
            }
            // reset
            DATA.diagResults = [];
            renderDiagnosis();
            setTimeout(step, 400);
        }

        // ============================================================
        // SOLUTIONS
        // ============================================================
        function renderSolutions() {
            const cat = document.getElementById('solCat').value;
            const q = document.getElementById('solSearch').value.toLowerCase();
            const list = DATA.solutions.filter(s => {
                if (cat !== 'all' && s.cat !== cat) return false;
                if (q && !s.name.toLowerCase().includes(q) && !s.desc.toLowerCase().includes(q)) return false;
                return true;
            });
            document.getElementById('solutionGrid').innerHTML = list.map(s => `
            <div class="solution-card" onclick="showSolutionDetail('${s.id}')">
              <div class="cat">${s.cat}</div>
              <div class="name">${s.name}</div>
              <div class="desc">${s.desc}</div>
              <div class="price">${s.price===0?'免费':`¥${s.price}/月`}</div>
              <div class="rating">⭐ ${s.rating} · ${s.users} 家企业</div>
            </div>
          `).join('');
            if (list.length === 0) {
                document.getElementById('solutionGrid').innerHTML =
                    '<div style="grid-column:1/-1;text-align:center;color:var(--gray-400);padding:40px 0;">暂无匹配方案</div>';
            }
        }

        function showSolutionDetail(id) {
            const s = DATA.solutions.find(x => x.id === id);
            if (!s) return;
            modalOpen('🏪 ' + s.name, `
            <div style="font-size:14px;line-height:1.7;">
              <p><strong>分类：</strong>${s.cat}</p>
              <p><strong>提供方：</strong>${s.vendor}</p>
              <p><strong>评分：</strong>⭐ ${s.rating} (${s.users} 家企业使用)</p>
              <p><strong>价格：</strong>${s.price===0?'免费':`¥${s.price}/月`}</p>
              <p style="margin-top:10px;">${s.desc}</p>
              <p style="margin-top:8px;color:var(--gray-500);font-size:13px;">该方案已通过安全合规审核，可申请试用。</p>
            </div>
          `, '📩 申请试用', function() { toast('✅ 试用申请已提交，1个工作日内联系您'); });
        }

        // ============================================================
        // SECURITY
        // ============================================================
        function renderSecurity() {
            const level = document.getElementById('secFilter').value;
            let list = DATA.events;
            if (level !== 'all') list = list.filter(e => e.level === level);

            const total = DATA.events.length;
            const high = DATA.events.filter(e => e.level === '严重' || e.level === '高危').length;
            const unhandled = DATA.events.filter(e => e.handled === 0).length;
            const today = DATA.events.filter(e => e.time.startsWith(new Date().toISOString().slice(0, 10))).length;

            document.getElementById('secStats').innerHTML = `
            <div class="stat-card"><div class="num">${total}</div><div class="label">事件总数</div></div>
            <div class="stat-card"><div class="num">${high}</div><div class="label">严重/高危</div></div>
            <div class="stat-card"><div class="num">${unhandled}</div><div class="label">未处理</div></div>
            <div class="stat-card"><div class="num">${today}</div><div class="label">今日事件</div></div>
          `;

            const tbody = document.getElementById('secTableBody');
            tbody.innerHTML = list.map((e, i) => `
            <tr data-idx="${i}" onclick="selectEvent(${i})" class="${DATA.selectedEvent===i?'selected':''}">
              <td><span style="font-weight:500;">${e.id}</span></td>
              <td>${e.time}</td>
              <td>${e.kind}</td>
              <td><span class="status-badge ${e.level==='严重'?'alarm':e.level==='高危'?'alarm':e.level==='中危'?'maintenance':e.level==='低危'?'pending':'running'}">${e.level}</span></td>
              <td>${e.source}</td>
            </tr>
          `).join('');
            if (DATA.selectedEvent !== null && DATA.selectedEvent >= list.length) DATA.selectedEvent = null;
            if (DATA.selectedEvent !== null && list.length > 0) {
                showEventDetail(DATA.selectedEvent, list);
            } else if (list.length > 0) {
                selectEvent(0);
            } else {
                document.getElementById('secDetail').innerHTML =
                    `<div class="title">🛡️ 事件详情</div><div style="color:var(--gray-400);font-size:13px;padding:12px 0;">无事件</div>`;
            }
        }

        function selectEvent(idx) {
            const list = getFilteredEvents();
            if (idx >= list.length) return;
            DATA.selectedEvent = idx;
            showEventDetail(idx, list);
            renderSecurity();
        }

        function getFilteredEvents() {
            const level = document.getElementById('secFilter').value;
            if (level === 'all') return DATA.events;
            return DATA.events.filter(e => e.level === level);
        }

        function showEventDetail(idx, list) {
            const e = list[idx];
            const el = document.getElementById('secDetail');
            el.innerHTML = `
            <div class="title">🛡️ ${e.id} - ${e.kind}</div>
            <div class="grid">
              <div class="item"><span class="k">时间</span><span class="v">${e.time}</span></div>
              <div class="item"><span class="k">级别</span><span class="v"><span class="status-badge ${e.level==='严重'?'alarm':e.level==='高危'?'alarm':e.level==='中危'?'maintenance':e.level==='低危'?'pending':'running'}">${e.level}</span></span></div>
              <div class="item"><span class="k">来源</span><span class="v">${e.source}</span></div>
              <div class="item"><span class="k">状态</span><span class="v">${e.handled?'✅ 已处理':'⏳ 未处理'}</span></div>
              <div class="item" style="grid-column:1/-1;"><span class="k">详情</span><span class="v">${e.detail}</span></div>
            </div>
            <div class="actions">
              ${e.handled===0?`<button class="btn success" onclick="handleEvent('${e.id}')">✅ 标记已处理</button>`:''}
              <button class="btn danger" onclick="deleteEvent('${e.id}')">🗑 删除记录</button>
            </div>
          `;
        }

        function handleEvent(id) {
            const e = DATA.events.find(x => x.id === id);
            if (e) {
                e.handled = 1;
                toast(`✅ 事件 ${e.id} 已标记为已处理`);
                renderSecurity();
            }
        }

        function deleteEvent(id) {
            const idx = DATA.events.findIndex(x => x.id === id);
            if (idx > -1) {
                DATA.events.splice(idx, 1);
                DATA.selectedEvent = null;
                toast(`🗑 事件已删除`);
                renderSecurity();
            }
        }

        function runSecurityScan() {
            const id = genId('EVT');
            DATA.events.unshift({
                id,
                time: new Date().toISOString().replace('T', ' ').slice(0, 19),
                kind: '安全告警',
                level: '中危',
                source: '系统-扫描',
                detail: '发现异常访问行为，已自动隔离，建议人工复核。',
                handled: 0
            });
            toast('🛡️ 安全扫描完成，新增 1 条中危告警');
            renderSecurity();
        }

        // ============================================================
        // UTILITIES
        // ============================================================
        function downloadCSV(csv, filename) {
            const blob = new Blob(['\uFEFF' + csv], { type: 'text/csv;charset=utf-8;' });
            const link = document.createElement('a');
            link.href = URL.createObjectURL(blob);
            link.download = filename;
            link.click();
            URL.revokeObjectURL(link.href);
        }

        // ============================================================
        // INIT
        // ============================================================
        function initApp() {
            renderDashboard();
            renderResources();
            renderCost();
            renderTickets();
            renderKnowledge();
            renderDiagnosis();
            renderSolutions();
            renderSecurity();
            // Auto-refresh dashboard every 30s
            setInterval(() => {
                if (document.getElementById('view-dashboard').classList.contains('active')) {
                    renderDashboard();
                }
            }, 30000);
        }

        // Pre-select first resource and ticket// 预选第一个资源和工单// 预选第一个资源和工单// 预选第一个资源和工单// 预选第一个资源和工单// 预选第一个资源和工单// 预选第一个资源和工单// 预选第一个资源和工单// 预选第一个资源和工单// 预选第一个资源和工单// 预选第一个资源和工单// 预选第一个资源和工单// 预选第一个资源和工单// 预选第一个资源和工单
        setTimeout(() => {
            if (DATA.resources.length) {
                DATA.selectedResource = 0;
            }
            if (DATA.tickets.length) {
                DATA.selectedTicket = 0;
            }
            if (DATA.events.length) {
                DATA.selectedEvent = 0;
            }
        }, 100);

        console.log('☁️ 云服务科技企业服务平台 V1.0 已加载');
        console.log('📊 数据:', DATA);
    </script>

</body>
</html>
