





# [카카오2018/자바] 3차 방금그곡

https://programmers.co.kr/learn/courses/30/lessons/17683



## 문제 설명

라디오를 자주 듣는 네오는 라디오에서 방금 나왔던 음악이 무슨 음악인지 궁금해질 때가 많다. 그럴 때 네오는 다음 포털의 '방금그곡' 서비스를 이용하곤 한다. 방금그곡에서는 TV, 라디오 등에서 나온 음악에 관해 제목 등의 정보를 제공하는 서비스이다.

네오는 자신이 기억한 멜로디를 가지고 방금그곡을 이용해 음악을 찾는다. 그런데 라디오 방송에서는 한 음악을 반복해서 재생할 때도 있어서 네오가 기억하고 있는 멜로디는 음악 끝부분과 처음 부분이 이어서 재생된 멜로디일 수도 있다. 반대로, 한 음악을 중간에 끊을 경우 원본 음악에는 네오가 기억한 멜로디가 들어있다 해도 그 곡이 네오가 들은 곡이 아닐 수도 있다. 그렇기 때문에 네오는 기억한 멜로디를 재생 시간과 제공된 악보를 직접 보면서 비교하려고 한다. 다음과 같은 가정을 할 때 네오가 찾으려는 음악의 제목을 구하여라.

- 방금그곡 서비스에서는 음악 제목, 재생이 시작되고 끝난 시각, 악보를 제공한다.
- 네오가 기억한 멜로디와 악보에 사용되는 음은 C, C#, D, D#, E, F, F#, G, G#, A, A#, B 12개이다.
- 각 음은 1분에 1개씩 재생된다. 음악은 반드시 처음부터 재생되며 음악 길이보다 재생된 시간이 길 때는 음악이 끊김 없이 처음부터 반복해서 재생된다. 음악 길이보다 재생된 시간이 짧을 때는 처음부터 재생 시간만큼만 재생된다.
- 음악이 00:00를 넘겨서까지 재생되는 일은 없다.
- 조건이 일치하는 음악이 여러 개일 때에는 라디오에서 재생된 시간이 제일 긴 음악 제목을 반환한다. 재생된 시간도 같을 경우 먼저 입력된 음악 제목을 반환한다.
- 조건이 일치하는 음악이 없을 때에는 `(None)`을 반환한다.



## 입력 형식

입력으로 네오가 기억한 멜로디를 담은 문자열 `m`과 방송된 곡의 정보를 담고 있는 배열 `musicinfos`가 주어진다.

- `m`은 음 `1`개 이상 `1439`개 이하로 구성되어 있다.
- musicinfos 는 100개 이하의 곡 정보를 담고 있는 배열로, 각각의 곡 정보는 음악이 시작한 시각, 끝난 시각, 음악 제목, 악보 정보가 ','로 구분된 문자열이다.
  - 음악의 시작 시각과 끝난 시각은 24시간 `HH:MM` 형식이다.
  - 음악 제목은 '`,`' 이외의 출력 가능한 문자로 표현된 길이 `1` 이상 `64` 이하의 문자열이다.
  - 악보 정보는 음 `1`개 이상 `1439`개 이하로 구성되어 있다.



## 출력 형식

조건과 일치하는 음악 제목을 출력한다.



## 문제 접근

<img src="https://tva1.sinaimg.cn/large/00831rSTgy1gcsdlbzudfj30ut0rsql7.jpg" alt="image-20200313172241274" style="zoom:70%;" />



## 코드 구현#1

```java
import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.ArrayList;
import java.util.Date;
import java.util.Iterator;
import java.util.regex.Matcher;
import java.util.regex.Pattern;

class Solution {

	public static class Music{
		public String title, melody, realMelody;
		public long playTime;
		
		public Music() {
			
		}
		public Music(String title, String melody, long playTime) {

			this.title = title;
			this.melody = melody;
			this.playTime = Math.abs(playTime);
			this.realMelody = "";
			int len = ((int) this.playTime) - this.melody.length();
			
			if(len<0) {
				this.realMelody = melody.substring(0,melody.length()+len);
			}else if(len>0) {
				int cnt = (int)this.playTime/melody.length();
				int res = (int)this.playTime%melody.length();
				
				for(int i=0; i<cnt; i++) {
					this.realMelody = this.realMelody.concat(melody);
					
				}
				this.realMelody = this.realMelody.concat(melody.substring(0,res));
			}else {
				this.realMelody = this.melody;
			}
		}		
	}
	
	public static long getPlayTime(String t1, String t2)  throws ParseException {
		long result = 0l;
		SimpleDateFormat sdf = new SimpleDateFormat("hh:mm");
		
		result = (sdf.parse(t2).getTime() - sdf.parse(t1).getTime())/60000;

		return result;
	}
	
	public static String changeStr(String m) {
	String result = m;	
	Pattern pattern = Pattern.compile(".(#)");
    
    Matcher matcher = pattern.matcher(m);
    while(matcher.find()) {
    	result = matcher.replaceFirst(matcher.group().replaceAll("\\#", "").toLowerCase());
  	  matcher = pattern.matcher(result);
  	 
    }
    return result;
	}
	public String solution(String m, String[] musicinfos) throws ParseException {
	      String answer = "(None)";
            long max = 0l;
	      ArrayList<Music> musicList = new ArrayList<>();
	      m = changeStr(m);
	      for(String music : musicinfos) {
	    	  String[] desc = music.split(",");
	    	  long playTime = getPlayTime(desc[1], desc[0]);
	    	  String title = desc[2];
	    	  String melody = changeStr(desc[3]);

	    	  musicList.add(new Music(title, melody, playTime));
	      }
	
	   
	      for(Music music : musicList) {
	    	 
	    	 if(music.realMelody.contains(m)&&max<music.playTime) {
	    		 max = music.playTime;
	    		 answer = music.title;
	    	 }
	     }
	      
	      return answer;
	  }
}
```



## 코드 구현 #2

```java
import java.util.*;
import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.regex.*;



class Solution {

	public static class Music{
		public String title, melody, realMelody;
		public long playTime;
		
		public Music() {
			// TODO Auto-generated constructor stub
		}
		public Music(String title, String melody, long playTime) {

			this.title = title;
			this.melody = melody;
			this.playTime = Math.abs(playTime);
			this.realMelody = "";
			
			
		}

	    public String getRealMelody() {
			int len = ((int) this.playTime) - this.melody.length();
			if(len<0) {
				this.realMelody = melody.substring(0,melody.length()+len);
			}else if(len>0) {
				int cnt = (int)this.playTime/melody.length();
				int res = (int)this.playTime%melody.length();
				
				for(int i=0; i<cnt; i++) {
					this.realMelody = this.realMelody.concat(melody);
					
				}
				this.realMelody = this.realMelody.concat(melody.substring(0,res));
			}else {
				this.realMelody = this.melody;
			}
			return realMelody;
		}
		
		
		
	}
	
	public static long getPlayTime(String t1, String t2)  throws ParseException {
		long result = 0l;
		SimpleDateFormat sdf = new SimpleDateFormat("hh:mm");
		
		result = (sdf.parse(t2).getTime() - sdf.parse(t1).getTime())/60000;

		return result;
	}
	
	public static String getMelody(String melody) {
		String m = melody;
		Pattern pattern = Pattern.compile(".(#)");
	    Matcher matcher = pattern.matcher(m);
		
		
		while(matcher.find()) {
			String key = matcher.group();
            String value = key.replaceAll("\\#", "").toLowerCase();
			m = m.replace(key, value);
			matcher = pattern.matcher(m);
		}
		
		return m;
		
	}
    
    
	public String solution(String m, String[] musicinfos) throws ParseException {
	      String answer = "(None)";
            long max = 0l;
	      ArrayList<Music> musicList = new ArrayList<>();
	      m = getMelody(m);
	      for(String music : musicinfos) {
	    	  String[] desc = music.split(",");
	    	  long playTime = getPlayTime(desc[1], desc[0]);
	    	  String title = desc[2];
	    	  String melody = getMelody(desc[3]);
	    	  musicList.add(new Music(title, melody, playTime));
	      }
	
	   
	      for(Music music : musicList) {
	    	 
	    	 if(music.getRealMelody().contains(m)&&max<music.playTime) {
	    		 max = music.playTime;
	    		 answer=music.title;
	    	 }
	     }
	      
	      
	    
	      return answer;
	  }
}
```

