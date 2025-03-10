<!DOCTYPE html>
<html>
<head>
    <style>
        body {
            display: flex;
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 0;
        }
        .sidebar {
            width: 20%;
            background-color: #f4f4f4;
            padding: 20px;
            box-sizing: border-box;
            height: 100vh;
        }
        .content {
            width: 80%;
            padding: 20px;
            box-sizing: border-box;
        }
        a {
            text-decoration: none;
            display: block;
            padding: 5px 0;
            color: #333;
        }
        a:hover {
            color: #007BFF;
        }
    </style>
</head>
<body>
    <div class="sidebar">
        <h3>菜单</h3>
        <a href="#section1">部分 1</a>
        <a href="#section2">部分 2</a>
        <a href="#section3">部分 3</a>
    </div>
    <div class="content">
        <h1 id="section1">部分 1</h1>
        <p>这里是部分 1 的内容。</p>
        <h1 id="section2">部分 2</h1>
        <p>这里是部分 2 的内容。</p>
        <h1 id="section3">部分 3</h1>
        <p>这里是部分 3 的内容。</p>
    </div>
</body>
</html>
