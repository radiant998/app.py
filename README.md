![image](https://github.com/user-attachments/assets/c784ec15-791e-4f4e-8f08-042ce0663c8f)
```
from flask import Flask, render_template, request, redirect, send_from_directory
import csv
import os
from werkzeug.utils import secure_filename

app = Flask(__name__)
app.config['UPLOAD_FOLDER'] = 'static/uploads'
os.makedirs(app.config['UPLOAD_FOLDER'], exist_ok=True)

# Route for the home page
@app.route('/')
def index():
    addressbook = []
    if os.path.exists('addbook.txt'):
        with open('addbook.txt', 'r', encoding='utf-8') as f:
            reader = csv.reader(f)
            addressbook = list(reader)
    return render_template('index.html', addressbook=addressbook)

# Route to handle form submission
@app.route('/add', methods=['POST'])
def add_contact():
    name = request.form['name']
    phone = request.form['phone']
    birthday = request.form['birthday']
    photo = request.files.get('photo')
    photo_filename = ''
    if photo and photo.filename:
        filename = secure_filename(photo.filename)
        # 파일명에 날짜시간을 추가해 중복 방지
        import datetime
        now = datetime.datetime.now().strftime('%Y%m%d%H%M%S')
        filename = f"{now}_{filename}"
        photo.save(os.path.join(app.config['UPLOAD_FOLDER'], filename))
        photo_filename = filename

    # Save to addbook.txt in CSV format
    with open('addbook.txt', 'a', newline='', encoding='utf-8') as file:
        writer = csv.writer(file)
        writer.writerow([name, phone, birthday, photo_filename])
        
    return redirect('/')

if __name__ == '__main__':
    app.run(debug=True)
```
