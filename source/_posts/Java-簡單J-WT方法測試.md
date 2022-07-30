---
title: Java 簡單JWT方法測試
date: 2022-07-31 02:17:12
tags: [java,jwt]
categories: Java
---

<!--more-->
## 實作

參考網路範例調整，簡單測試jwt功能，不要直接用下面程式喔。

```java
package  util;

import java.io.Serializable;
import java.time.Instant;
import java.util.Date;
import java.util.HashMap;
import java.util.Map;

import javax.security.auth.message.AuthException;

import org.springframework.stereotype.Component;

import io.jsonwebtoken.ExpiredJwtException;
import io.jsonwebtoken.Jwts;
import io.jsonwebtoken.MalformedJwtException;
import io.jsonwebtoken.SignatureAlgorithm;
import io.jsonwebtoken.SignatureException;
import io.jsonwebtoken.UnsupportedJwtException;

@Component
public class JwtTokenUtils implements Serializable {

    /**
	 * 
	 */
	private static final long serialVersionUID = 6307640988200151725L;
	private static final long EXPIRATION_TIME = 10 * 60 * 1000;
    /**
     * JWT SECRET KEY
     */
    private static final String SECRET = "PBCIKEY";

    /**
     * 簽發JWT
     */
    public String generateToken(Map<String, String> userDetails) {
        Map<String, Object> claims = new HashMap<>();
        claims.put( "userName", userDetails.get("userName") );

        return Jwts.builder()
                .setClaims( claims )
                .setExpiration( new Date( Instant.now().toEpochMilli() + EXPIRATION_TIME  ) )
                .signWith( SignatureAlgorithm.HS512, SECRET )
                .compact();
    }

    /**
     * 驗證JWT
     */
    public void validateToken(String token) throws AuthException {
        try {
            Jwts.parser()
                    .setSigningKey( SECRET )
                    .parseClaimsJws( token );
        } catch (SignatureException e) {
            throw new AuthException("Invalid JWT signature.");
        }
        catch (MalformedJwtException e) {
            throw new AuthException("Invalid JWT token.");
        }
        catch (ExpiredJwtException e) {
            throw new AuthException("Expired JWT token");
        }
        catch (UnsupportedJwtException e) {
            throw new AuthException("Unsupported JWT token");
        }
        catch (IllegalArgumentException e) {
            throw new AuthException("JWT token compact of handler are invalid");
        }
    }
    
    public static void main(String[] argv) {
    	Map<String,String> userMap = new HashMap<>();
    	userMap.put("userName","HelloUser");
    	JwtTokenUtils jwtUtils = new JwtTokenUtils();
    	String token = jwtUtils.generateToken(userMap);
    	
    	System.out.println("token:" + token);
    	
    	try {
    		jwtUtils.validateToken(token);
            String user = (String) jwtUtils.getUserName(token);
            System.out.println("取得 user:" + user);
		} catch (AuthException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
    }

	/**
	 * @param token
	 * @return
	 */
	private Object getUserName(String token) {
		return Jwts.parser()
		        .setSigningKey(SECRET)
		        .parseClaimsJws(token)
		        .getBody()
		        .get("userName");
	}
}
```

## 參考文章

[Day 29 - Spring Boot 想要資料令牌要先帶來!－ JWT - iT 邦幫忙::一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10250968)


[Java - JSON Web Tokens (JWT) 示範 | Kenny's Blog](https://blog.kennycoder.io/2020/01/14/Java-JSON-Web-Tokens-JWT-%E7%A4%BA%E7%AF%84/)


[[筆記] 透過 JWT 實作驗證機制. 以 JWT（JSON Web Tokens）實作驗證機制 | by Mike Huang | 麥克的半路出家筆記 | Medium](https://medium.com/%E9%BA%A5%E5%85%8B%E7%9A%84%E5%8D%8A%E8%B7%AF%E5%87%BA%E5%AE%B6%E7%AD%86%E8%A8%98/%E7%AD%86%E8%A8%98-%E9%80%8F%E9%81%8E-jwt-%E5%AF%A6%E4%BD%9C%E9%A9%97%E8%AD%89%E6%A9%9F%E5%88%B6-2e64d72594f8)

