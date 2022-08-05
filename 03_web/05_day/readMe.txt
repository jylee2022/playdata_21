
//목록보기
	public PageDTO list(Connection con,String searchName, String searchValue, int curPage){
		PageDTO pageDTO = new PageDTO();
		

		////////////////////////////////////////////////////////
		List<BoardDTO> list = new ArrayList<BoardDTO>();
		PreparedStatement pstmt = null;
		ResultSet rs = null;
		try {
			StringBuffer buffer = new StringBuffer();
			buffer.append("SELECT num, title, author, content, to_char(writeday, 'YYYY/MM/dd') as writeday , readcnt");
			buffer.append(" FROM");
			buffer.append(" (");
			buffer.append(" SELECT SEQ, num, title, author, content, writeday, readcnt");
			buffer.append(" FROM");
			buffer.append(" (");
			buffer.append(" SELECT ROWNUM AS SEQ, num, title, author, content, writeday, readcnt");
			buffer.append(" FROM");
			buffer.append(" (");
			buffer.append(" SELECT num, title, author, content, writeday , readcnt");
			buffer.append(" FROM board");
			
			if("title".equals(searchName) && searchValue != null) {
				buffer.append(" where title LIKE ?");
				buffer.append(" ORDER BY num DESC");
				buffer.append(" )");
				buffer.append(" )");
				buffer.append(" WHERE SEQ >= 2");
				buffer.append(" )");
				buffer.append(" WHERE ROWNUM <= 3");
				pstmt = con.prepareStatement(buffer.toString());
				pstmt.setString(1, "%"+searchValue+"%");

			}else if("author".equals(searchName) && searchValue != null){
				buffer.append(" where author LIKE ?");
				buffer.append(" ORDER BY num DESC");
				buffer.append(" )");
				buffer.append(" )");
				buffer.append(" WHERE SEQ >= 2");
				buffer.append(" )");
				buffer.append(" WHERE ROWNUM <= 3");
				pstmt = con.prepareStatement(buffer.toString());
				pstmt.setString(1, "%"+searchValue+"%");

			}else {
				buffer.append(" ORDER BY num DESC");
				buffer.append(" )");
				buffer.append(" )");
				buffer.append(" WHERE SEQ >= 1");
				buffer.append(" )");
				buffer.append(" WHERE ROWNUM <= 3");
				pstmt = con.prepareStatement(buffer.toString());
			}
			System.out.println("#################################");
			System.out.println(buffer.toString());
			System.out.println("#################################");
			rs = pstmt.executeQuery();
			while(rs.next()) {
				int num = rs.getInt("num");
			    String title = rs.getString("title");
				String author = rs.getString("author");
				String writeday = rs.getString("writeday");
				int readcnt = rs.getInt("readcnt");
				// 나중에 Builder 패턴
				BoardDTO dto = new BoardDTO();
				dto.setNum(num);
				dto.setTitle(title);
				dto.setAuthor(author);
				dto.setWriteday(writeday);
				dto.setReadcnt(readcnt);
				list.add(dto);
			}
		}catch(SQLException e) {
			e.printStackTrace();
		}finally {
			try {
				if(rs!=null)rs.close();
				if(pstmt!=null)pstmt.close();
			} catch (SQLException e) {
				e.printStackTrace();
			}	
		}
		////////////////////////////////////////////////////////
		pageDTO.setList(list);
		
		return pageDTO;
		
	}//end list
