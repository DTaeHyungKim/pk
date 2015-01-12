package exam.mvc.controller;

import java.io.File;
import java.io.IOException;
import java.util.List;

import javax.swing.plaf.multi.MultiFileChooserUI;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.ModelAttribute;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.ResponseBody;
import org.springframework.web.multipart.MultipartFile;

import exam.mvc.dto.Item;
import exam.mvc.service.ItemService;

@Controller
@RequestMapping("/item")
public class ItemController {
	@Autowired
	private ItemService itemService;
	 
	@ModelAttribute
	public Item newItem(){
		 
		return new Item();
	}
	
	@RequestMapping(value="/add", method=RequestMethod.POST)
	public String add(@RequestParam("itemId") String itemId,
			         @RequestParam("name") String name,
			         @RequestParam("image") MultipartFile image){
		System.out.println("add");
		System.out.println(itemId);
		System.out.println(image.getOriginalFilename());
		
		Item item = new Item();
		item.setitemId(itemId);
		item.setName(name);
		if(!image.isEmpty()){
			
			String root = System.getProperty("catalina.home");
			
			File dir= new File(root+"/upload");
			if(!dir.exists())
				dir.mkdir();
			String fileName=dir.getAbsolutePath()+"\\"+image.getOriginalFilename();
			File uploadFile = new File(fileName);
			try {
				image.transferTo(uploadFile);
			} catch (IllegalStateException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			} catch (IOException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
		
			item.setImage(image.getOriginalFilename());
		}else{
			item.setImage("");
		}
		
		int result = itemService.insertItem(item);
		
		
		
		return "redirect:jsonlist";
	}
	
	@RequestMapping(value="/list", method=RequestMethod.GET)
	public String list(Model model){
		 
		List<Item> list = itemService.selectItem();
		model.addAttribute("itemlist", list);
		System.out.println("list");
		return "item";
	}
	
	@RequestMapping(value="/jsonlist", method=RequestMethod.GET)
	public @ResponseBody List<Item> jsonlist(){
		
		List<Item> list = itemService.selectItem();
		System.out.println("jsonlist");
		
		
		return list;
	}
}
