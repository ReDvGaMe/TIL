# Programmers - 2018 KAKAO BLIND RECRUITMENT. [3차] 방금그곡

## 1. 문제  
https://programmers.co.kr/learn/courses/30/lessons/17683
## 2. 접근 방법  

## 3. 코드  
```
#include <string>
#include <vector>

using namespace std;

struct music
{
    int _len;
    string _musicinfos = "";
    string _title = "";
    string _melody = "";
    string _playedmelody = "";

    int setTitle()
    {
        // 노래 제목
        int idx = 12;
        for (idx; _musicinfos[idx] != ','; idx++)
            _title += _musicinfos[idx];

        return idx;
    }

    string make_melody(string str)
    {
        string melody = "";
        // 악보
        for (int i = 0; i < str.size(); i++)
        {
            if (i + 1 != str.size() && str[i + 1] == '#')
            {
                melody += tolower(str[i]);
                i++;
            }
            else    melody += str[i];
        }

        return melody;
    }

    string make_playedmelody()
    {
        string playedmelody = "";
        // 노래가 시간동안 틀어졌을 때 악보
        int idx = 0;
        for (int i = 0; i < _len; i++)
        {
            playedmelody += _melody[idx];
            idx = (idx + 1 == _melody.size()) ? 0 : idx + 1;
        }

        return playedmelody;
    }

    music()
    {
        _title = "(None)";
    }

    music(string musicinfos)
    {
        _musicinfos = musicinfos;

        // 노래가 재생된 시간 구하기
        int starthour = stoi(musicinfos.substr(0, 2));
        int startmin = stoi(musicinfos.substr(3, 2));
        int endhour = stoi(musicinfos.substr(6, 2));
        int endmin = stoi(musicinfos.substr(9, 2));

        int h = endhour - starthour;
        int min = endmin - startmin;
        _len = (h * 60) + min;

        int idx = setTitle();
        _melody = make_melody(musicinfos.substr(idx + 1));
        _playedmelody = make_playedmelody();
    }
};

string solution(string m, vector<string> musicinfos)
{
    vector<music> musiclist;
    vector<string> findmusic;
    music ans;
    m = ans.make_melody(m);
    for (int i = 0; i < musicinfos.size(); i++)
    {
        musiclist.push_back(music(musicinfos[i]));
        // 내가 들은 멜로디가 포함되어 있다면
        if (musiclist[i]._playedmelody.find(m) != string::npos)
        {
            if (ans._title == "(None)" || musiclist[i]._len > ans._len)
                ans = musiclist[i];
        }
    }

    return ans._title;
}
```
## 4. 결과
- 결과 : Pass