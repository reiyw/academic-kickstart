+++
title = "言語処理100本ノックを Rust で解く | 第1章"

date = 2018-04-10
lastmod = 2018-04-10
draft = false

tags = ["rust", "100本ノック"]
summary = "[言語処理100本ノック](http://www.cl.ecei.tohoku.ac.jp/nlp100/) を勉強を兼ねて Rust で解き始めたので，記録としてここにも残していきます．"

[header]
image = ""
caption = ""
+++

[言語処理100本ノック](http://www.cl.ecei.tohoku.ac.jp/nlp100/) を勉強を兼ねて Rust で解き始めたので，記録としてここにも残していきます．解説とかは気が向いたら追記していきます．

```rust
extern crate rand;

use std::collections::HashMap;
use std::collections::HashSet;
use std::char;
use self::rand::{thread_rng, Rng};

pub fn problem00(s: &str) -> String {
    s.chars().rev().collect()
}

pub fn problem01(s: &str) -> String {
    s.chars()
        .enumerate()
        .filter_map(|(i, char)| if i % 2 == 0 { Some(char) } else { None })
        .collect()
}

pub fn problem02(s1: &str, s2: &str) -> String {
    s1.chars()
        .zip(s2.chars())
        .map(|(c1, c2)| format!("{}{}", c1, c2))
        .collect()
}

pub fn problem03() -> Vec<i32> {
    let s = "Now I need a drink, alcoholic of course, after the heavy lectures involving quantum mechanics.";
    s.split(' ')
        .map(|w| w.trim_matches(|c| c == ',' || c == '.'))
        .map(|w| w.len() as i32)
        .collect()
}

pub fn problem04() -> HashMap<String, i32> {
    let s = "Hi He Lied Because Boron Could Not Oxidize Fluorine. New Nations Might Also Sign Peace Security Clause. Arthur King Can.";
    let indices: HashSet<_> = [0, 4, 5, 6, 7, 8, 14, 15, 18].iter().cloned().collect();
    s.split(' ')
        .enumerate()
        .map(|(i, w)| {
            if indices.contains(&i) {
                (w.chars().nth(0).unwrap().to_string(), (i + 1) as i32)
            } else {
                (
                    w.chars().collect::<Vec<char>>()[0..2]
                        .into_iter()
                        .collect::<String>(),
                    (i + 1) as i32,
                )
            }
        })
        .collect()
}

// pub fn problem05<T: AsRef<str> + Clone>(words: &[T], n: i32) -> Vec<Vec<T>> {
pub fn problem05(words: Vec<String>, n: i32) -> Vec<Vec<String>> {
    words
        .windows(n as usize)
        .map(|chunk| chunk.to_vec())
        .collect()
}

pub fn problem06(words: Vec<String>, n: i32) -> HashSet<Vec<String>> {
    words
        .windows(n as usize)
        .map(|chunk| chunk.to_vec())
        .collect()
}

pub fn problem07(x: i32, y: &str, z: f64) -> String {
    format!("{}時の{}は{}", x, y, z)
}

pub fn cipher(string: &str) -> String {
    let mut b = [0; 1];
    string
        .chars()
        .map(|c| {
            if c.is_ascii_lowercase() {
                c.encode_utf8(&mut b);
                char::from_u32((219 - b[0]) as u32).unwrap()
            } else {
                c
            }
        })
        .collect()
}

pub fn problem09(string: &str) -> String {
    let mut rng = thread_rng();
    string
        .split(' ')
        .map(|word| {
            if word.len() > 4 {
                let chars: Vec<char> = word.chars().collect();
                let mut inner_chars: Vec<char> = chars[1..word.len() - 1]
                    .into_iter()
                    .collect::<String>()
                    .chars()
                    .collect();
                let slice = inner_chars.as_mut_slice();
                rng.shuffle(slice);
                let mut shuffled: Vec<char> = Vec::new();
                shuffled.push(chars[0]);
                shuffled.extend_from_slice(slice);
                shuffled.push(chars[word.len() - 1]);
                shuffled.into_iter().collect::<String>()
            } else {
                word.to_string()
            }
        })
        .collect::<Vec<_>>()
        .join(" ")
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_problem00() {
        assert_eq!("desserts", problem00("stressed"));
        assert_eq!("ーカトパ", problem00("パトカー"));
    }

    #[test]
    fn test_problem01() {
        assert_eq!("パトカー", problem01("パタトクカシー"));
        assert_eq!("srse", problem01("stressed"));
    }

    #[test]
    fn test_problem02() {
        assert_eq!(
            "パタトクカシーー",
            problem02("パトカー", "タクシー")
        );
    }

    #[test]
    fn test_problem03() {
        assert_eq!(
            vec![3, 1, 4, 1, 5, 9, 2, 6, 5, 3, 5, 8, 9, 7, 9],
            problem03()
        );
    }

    #[test]
    fn test_problem04() {
        let map = problem04();
        assert_eq!(1, map["H"]);
        assert_eq!(2, map["He"]);
        assert_eq!(20, map["Ca"]);
    }

    #[test]
    fn test_problem05() {
        assert_eq!(
            vec![vec!["I", "am"], vec!["am", "an"], vec!["an", "NLPer"]],
            problem05(
                "I am an NLPer"
                    .split(' ')
                    .map(|word| word.to_string())
                    .collect::<Vec<_>>(),
                2
            )
        );
        assert_eq!(
            vec![
                vec!["I", " "],
                vec![" ", "a"],
                vec!["a", "m"],
                vec!["m", " "],
                vec![" ", "a"],
                vec!["a", "n"],
                vec!["n", " "],
                vec![" ", "N"],
                vec!["N", "L"],
                vec!["L", "P"],
                vec!["P", "e"],
                vec!["e", "r"],
            ],
            problem05(
                "I am an NLPer"
                    .chars()
                    .map(|c| c.to_string())
                    .collect::<Vec<_>>(),
                2
            )
        );
    }

    #[test]
    fn test_problem06() {
        let x = problem06(
            "paraparaparadise"
                .chars()
                .map(|c| c.to_string())
                .collect::<Vec<_>>(),
            2,
        );
        let y = problem06(
            "paragraph"
                .chars()
                .map(|c| c.to_string())
                .collect::<Vec<_>>(),
            2,
        );
        assert_eq!(
            [
                vec!["p".to_string(), "a".to_string()],
                vec!["a".to_string(), "r".to_string()],
                vec!["r".to_string(), "a".to_string()],
                vec!["a".to_string(), "p".to_string()],
                vec!["a".to_string(), "d".to_string()],
                vec!["d".to_string(), "i".to_string()],
                vec!["i".to_string(), "s".to_string()],
                vec!["s".to_string(), "e".to_string()],
                vec!["a".to_string(), "g".to_string()],
                vec!["g".to_string(), "r".to_string()],
                vec!["p".to_string(), "h".to_string()],
            ].iter()
                .collect::<HashSet<_>>(),
            x.union(&y).collect::<HashSet<_>>()
        );
        assert_eq!(
            [
                vec!["p".to_string(), "a".to_string()],
                vec!["a".to_string(), "r".to_string()],
                vec!["r".to_string(), "a".to_string()],
                vec!["a".to_string(), "p".to_string()],
            ].iter()
                .collect::<HashSet<_>>(),
            x.intersection(&y).collect::<HashSet<_>>()
        );
        assert_eq!(
            [
                vec!["a".to_string(), "d".to_string()],
                vec!["d".to_string(), "i".to_string()],
                vec!["i".to_string(), "s".to_string()],
                vec!["s".to_string(), "e".to_string()],
            ].iter()
                .collect::<HashSet<_>>(),
            x.difference(&y).collect::<HashSet<_>>()
        );
        assert_eq!(
            [
                vec!["a".to_string(), "g".to_string()],
                vec!["g".to_string(), "r".to_string()],
                vec!["p".to_string(), "h".to_string()],
            ].iter()
                .collect::<HashSet<_>>(),
            y.difference(&x).collect::<HashSet<_>>()
        );
        assert!(x.contains(&vec!["s".to_string(), "e".to_string()]));
        assert!(!y.contains(&vec!["s".to_string(), "e".to_string()]));
    }

    #[test]
    fn test_problem07() {
        assert_eq!("12時の気温は22.4", problem07(12, "気温", 22.4));
    }

    #[test]
    fn test_problem08() {
        assert_eq!("A gvhg", cipher("A test"));
        assert_eq!("A test", cipher(&cipher("A test")));
    }

    #[test]
    fn test_problem09() {
        assert_eq!("A test", problem09("A test"));
    }
}
```
