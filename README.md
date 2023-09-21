# BestBusinessDone
I aspire to create a website to blog and allow people to blog promoting themselves. To join the Blogging market and making a available source of income in a unique way. Encourage people to be creative for money. Advertising passive income.

>pip install Flask


from flask import Flask, render_template, request, redirect, url_for
import sqlite3

app = Flask('Business Done')

# Initialize the SQLite database
conn = sqlite3.connect('blog.bd')
conn.execute('''
    CREATE TABLE IF NOT EXISTS posts (
        id INTEGER PRIMARY KEY,
        title TEXT,
        content TEXT
    )
''')
conn.execute('''
    CREATE TABLE IF NOT EXISTS comments (
        id INTEGER PRIMARY KEY,
        post_id INTEGER,
        text TEXT
    )
''')
conn.close()

@app.route('/')
def index():
    conn = sqlite3.connect('blog.db')
    cursor = conn.cursor()
    cursor.execute('SELECT * FROM posts')
    posts = cursor.fetchall()
    conn.close()
    return render_template('index.html', posts=posts)

@app.route('/post/<int:post_id>')
def post(post_id):
    conn = sqlite3.connect('blog.db')
    cursor = conn.cursor()
    cursor.execute('SELECT * FROM posts WHERE id = ?', (post_id,))
    post = cursor.fetchone()
    cursor.execute('SELECT * FROM comments WHERE post_id = ?', (post_id,))
    comments = cursor.fetchall()
    conn.close()
    return render_template('post.html', post=post, comments=comments)

@app.route('/add_comment/<int:post_id>', methods=['POST'])
def add_comment(post_id):
    text = request.form['comment_text']
    conn = sqlite3.connect('blog.db')
    cursor = conn.cursor()
    cursor.execute('INSERT INTO comments (post_id, text) VALUES (?, ?)', (post_id, text))
    conn.commit()
    conn.close()
    return redirect(url_for('post', post_id=post_id))

if 'Business Done' == '__main__':
    app.run(debug=True)
