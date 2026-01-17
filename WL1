import tkinter as tk
from tkinter import messagebox
import hashlib
import sys

class Locker:
    def __init__(self, password_hash=None):
        self.root = tk.Tk()
        self.root.title("System Locked")
        
        # Полноэкранный режим
        self.root.attributes('-fullscreen', True)
        self.root.attributes('-topmost', True)
        
        # Убираем декорации окна
        self.root.overrideredirect(True)
        
        # Получаем размеры экрана
        screen_width = self.root.winfo_screenwidth()
        screen_height = self.root.winfo_screenheight()
        self.root.geometry(f"{screen_width}x{screen_height}+0+0")
        
        # Хеш пароля (по умолчанию "12345")
        self.password_hash = password_hash or self.hash_password("12345")
        
        # Счетчик неудачных попыток
        self.failed_attempts = 0
        self.max_attempts = 5
        
        # Настройка UI
        self.setup_ui()
        
        # Блокировка событий
        self.setup_bindings()
        
        # Захват фокуса
        self.root.focus_force()
        self.root.grab_set()
        
    def hash_password(self, password):
        """Хеширование пароля для безопасности"""
        return hashlib.sha256(password.encode()).hexdigest()
    
    def setup_ui(self):
        """Создание интерфейса"""
        # Фон
        self.root.configure(bg='#1a1a1a')
        
        # Главный контейнер
        main_frame = tk.Frame(self.root, bg='#1a1a1a')
        main_frame.place(relx=0.5, rely=0.5, anchor='center')
        
        # Иконка замка (символ)
        lock_label = tk.Label(
            main_frame, 
            text="🔒", 
            font=("Arial", 72),
            bg='#1a1a1a',
            fg='#ffffff'
        )
        lock_label.pack(pady=20)
        
        # Заголовок
        title_label = tk.Label(
            main_frame,
            text="Компьютер заблокирован",
            font=("Arial", 24, "bold"),
            bg='#1a1a1a',
            fg='#ffffff'
        )
        title_label.pack(pady=10)
        
        # Инструкция
        info_label = tk.Label(
            main_frame,
            text="Введите пароль для разблокировки",
            font=("Arial", 12),
            bg='#1a1a1a',
            fg='#aaaaaa'
        )
        info_label.pack(pady=5)
        
        # Поле ввода пароля
        self.password_entry = tk.Entry(
            main_frame,
            show="●",
            font=("Arial", 16),
            width=25,
            bg='#2a2a2a',
            fg='#ffffff',
            insertbackground='#ffffff',
            relief='flat',
            bd=2
        )
        self.password_entry.pack(pady=20, ipady=8)
        self.password_entry.focus_set()
        
        # Кнопка разблокировки
        self.submit_button = tk.Button(
            main_frame,
            text="Разблокировать",
            command=self.check_password,
            font=("Arial", 12, "bold"),
            bg='#0078d4',
            fg='#ffffff',
            activebackground='#005a9e',
            activeforeground='#ffffff',
            relief='flat',
            cursor='hand2',
            width=20,
            height=2
        )
        self.submit_button.pack(pady=10)
        
        # Метка для сообщений об ошибках
        self.error_label = tk.Label(
            main_frame,
            text="",
            font=("Arial", 10),
            bg='#1a1a1a',
            fg='#ff4444'
        )
        self.error_label.pack(pady=5)
        
        # Информация о попытках
        self.attempts_label = tk.Label(
            main_frame,
            text="",
            font=("Arial", 9),
            bg='#1a1a1a',
            fg='#888888'
        )
        self.attempts_label.pack(pady=5)
        
    def setup_bindings(self):
        """Настройка обработчиков событий"""
        # Блокировка закрытия окна
        self.root.protocol("WM_DELETE_WINDOW", self.disable_event)
        
        # Блокировка комбинаций клавиш
        self.root.bind('<Escape>', self.disable_event)
        self.root.bind('<Alt-F4>', self.disable_event)
        self.root.bind('<Control-c>', self.disable_event)
        self.root.bind('<Control-x>', self.disable_event)
        self.root.bind('<Control-v>', self.disable_event)
        self.root.bind('<F11>', self.disable_event)
        
        # Enter для отправки
        self.password_entry.bind('<Return>', lambda e: self.check_password())
        
        # Предотвращение потери фокуса
        self.root.bind('<FocusOut>', lambda e: self.root.focus_force())
        
    def disable_event(self, event=None):
        """Блокировка событий"""
        return "break"
    
    def check_password(self):
        """Проверка введенного пароля"""
        password = self.password_entry.get()
        
        if not password:
            self.show_error("Введите пароль")
            return
        
        if self.hash_password(password) == self.password_hash:
            messagebox.showinfo(
                "Успех", 
                "Компьютер разблокирован!",
                parent=self.root
            )
            self.unlock()
        else:
            self.failed_attempts += 1
            remaining = self.max_attempts - self.failed_attempts
            
            if remaining > 0:
                self.show_error(f"Неверный пароль!")
                self.attempts_label.config(
                    text=f"Осталось попыток: {remaining}"
                )
                self.password_entry.delete(0, tk.END)
                self.password_entry.focus_set()
            else:
                self.show_error("Превышено количество попыток!")
                self.submit_button.config(state='disabled')
                messagebox.showerror(
                    "Блокировка",
                    "Слишком много неудачных попыток.\n"
                    "Обратитесь к администратору.",
                    parent=self.root
                )
    
    def show_error(self, message):
        """Отображение сообщения об ошибке"""
        self.error_label.config(text=message)
        self.root.after(3000, lambda: self.error_label.config(text=""))
    
    def unlock(self):
        """Разблокировка и закрытие приложения"""
        self.root.grab_release()
        self.root.destroy()
    
    def run(self):
        """Запуск приложения"""
        self.root.mainloop()


def main():
    """Главная функция"""
    # Можно задать свой пароль через хеш
    # Например, для пароля "mysecret":
    # custom_hash = hashlib.sha256("mysecret".encode()).hexdigest()
    # locker = Locker(password_hash=custom_hash)
    
    locker = Locker()  # По умолчанию пароль "12345"
    locker.run()


if __name__ == "__main__":
    main()
